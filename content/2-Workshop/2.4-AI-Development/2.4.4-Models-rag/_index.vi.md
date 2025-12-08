---
title : "AI Models & RAG Pipeline"
weight : 4
chapter : false
pre : " <b> 2.4.4. </b> "
---

Hệ thống sử dụng **Amazon Bedrock** làm cổng kết nối tập trung (Unified API), giúp đơn giản hóa việc quản lý version và bảo mật credentials.

## 1. Phân tích lựa chọn Model

### Embedding: Cohere Embed Multilingual v3
* **ID:** `cohere.embed-multilingual-v3`
* **Tại sao là Cohere mà không phải Titan?**
    * **Xử lý tiếng Việt/Hán-Việt:** Cohere v3 được huấn luyện trên tập dữ liệu đa ngôn ngữ lớn, hiểu tốt các từ ngữ Hán-Việt đặc thù trong huyền học (như "Thien Di", "Phu The") mà các model phương Tây thường hiểu sai.
    * **Input Type:** Hỗ trợ tham số `input_type="search_query"` (cho câu hỏi user) và `"search_document"` (cho database), giúp tối ưu hóa không gian vector cho việc tìm kiếm.
    * **Dimension:** 1024 chiều - Đây là điểm ngọt (sweet spot) cân bằng giữa độ chính xác ngữ nghĩa và chi phí lưu trữ trên Pinecone.

### Generative LLM: Amazon Nova Pro
* **ID:** `amazon.nova-pro-v1:0`
* **Sức mạnh suy luận (Reasoning):**
    * Thay vì chỉ tóm tắt văn bản, Nova Pro có khả năng **xâu chuỗi logic**. Ví dụ: Từ 3 lá bài Tarot rời rạc (Quá khứ - Hiện tại - Tương lai), model có thể liên kết chúng thành một câu chuyện nhân quả mạch lạc.
* **Cấu hình tham số (Inference Config):**
    * `temperature: 0.3`: Giữ ở mức thấp để AI bám sát vào Knowledge Base, tránh "sáng tạo" ra các kiến thức sai lệch (Hallucination).
    * `topP: 0.9`: Cho phép một chút linh hoạt trong cách diễn đạt để lời khuyên nghe tự nhiên, giống người hơn.

## 2. Triển khai trên AWS Lambda

Dưới đây là đoạn code mẫu Python (`boto3`) được sử dụng trong Lambda Function để thực hiện luồng RAG: Embed câu hỏi -> (Search Pinecone) -> Gửi Prompt cho Nova Pro.

```python
import boto3
import json
import os

# Khởi tạo Bedrock Runtime client
bedrock = boto3.client(
    service_name='bedrock-runtime', 
    region_name='us-east-1'
)

def generate_embedding(text):
    """
    Bước 1: Chuyển đổi câu hỏi người dùng thành Vector
    """
    response = bedrock.invoke_model(
        modelId='cohere.embed-multilingual-v3',
        contentType='application/json',
        accept='application/json',
        body=json.dumps({
            "texts": [text],
            "input_type": "search_query" # Tối ưu cho query
        })
    )
    result = json.loads(response['body'].read())
    return result['embeddings'][0]

def query_llm(context_chunk, user_question, history):
    """
    Bước 2: Gửi Context + Câu hỏi cho Amazon Nova Pro
    """
    # System Prompt: Định hình nhân cách chuyên gia
    system_prompt = """Bạn là một chuyên gia Tarot và Chiêm tinh học tận tâm. 
    Hãy trả lời câu hỏi dựa trên thông tin được cung cấp trong thẻ <context>. 
    Nếu thông tin không đủ, hãy thành thật nói rằng bạn không biết, đừng tự bịa ra."""

    # Construct Message Payload (Nova Pro structure)
    prompt_payload = {
        "system": [{"text": system_prompt}],
        "messages": [
            # Có thể chèn lịch sử chat vào đây
            {"role": "user", "content": [{"text": f"Context: {context_chunk}\n\nQuestion: {user_question}"}]}
        ],
        "inferenceConfig": {
            "temperature": 0.3, # Giảm ảo giác
            "maxTokens": 1000
        }
    }

    response = bedrock.invoke_model(
        modelId='amazon.nova-pro-v1:0',
        body=json.dumps(prompt_payload)
    )
    
    result = json.loads(response['body'].read())
    return result['output']['message']['content'][0]['text']