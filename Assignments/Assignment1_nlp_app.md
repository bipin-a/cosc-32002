# **Assignment 1: NLP Application**

## **Objective**
Develop and deploy a machine learning application as an API that addresses a specific natural language processing task. This assignment will test your ability to implement the full development lifecycle from model selection and training to API deployment and performance monitoring.

## **Core Requirements**

### **1. Select a Prediction Task**
Choose one of the following NLP tasks to implement:
- **Paragraph Similarity Analysis**: Develop a model that measures the similarity between two input paragraphs based on your defined similarity metrics.
- **Part of Speech Tagging**: Implement a model using Hidden Markov Models (HMM) or the Viterbi Algorithm to identify and label parts of speech in a sentence.
- **Next Word Prediction**: Create a model (Neural Network or RNN) that predicts the most likely next word given a sequence of words.

### **2. Dataset Selection**
Select an appropriate text corpus to train your model. You may use any dataset from repositories such as Hugging Face (https://huggingface.co/datasets?modality=modality:text&sort=trending) or another source of your choosing. Document your dataset selection and preprocessing steps.

### **3. Define Your API's Users**
Clearly identify and document:
- **Target User Profile**: Who will be using your API and for what purpose
- **Expected Usage Volume**: Estimate the number of requests per day
- **User Requirements**: Specify performance needs (e.g., response time requirements, batch vs. real-time processing)

### **4. Model Development and Training**
Implement your chosen NLP model:
- Document your model architecture and training approach
- Evaluate and report key performance metrics
- Justify your model selection and parameter choices

### **5. API Service Performance**
Implement monitoring for your API service and document:
- **Response Time**: Average and maximum processing duration per request
- **Memory Consumption**: Minimum and maximum memory usage
- **Request Capacity**: Maximum number of concurrent requests your API can handle

### **6. User Interaction Design**
Define and implement the user interaction flow:
- **Input Method**: Specify how users will submit data (e.g., textbox, file upload)
- **Output Format**: Determine how results will be presented (e.g., JSON response, visualizations)

### **7. Data Contract Specification**
Use Pydantic to create a formal data contract that defines:
- **Input Validation**: Types, constraints, and required fields
- **Response Structure**: Expected output format and fields

### **8. Deployment**
- **Containerize** your application using Docker
- **Deploy** to either Google Cloud Run or AWS Lambda
- **Document** your deployment configuration and process

## **Submission Requirements**

### **Code Repository**
Create a GitHub repository containing:
- **Complete Source Code**: Model training, API implementation, and deployment scripts
- **README**: Clear setup instructions, user definition, and interaction diagram
- **API Documentation**: Generated via the `/docs` endpoint

### **Video Presentation (50% of Grade)**
Create a 10-minute (maximum) video presentation that includes:
- Face cam recording of yourself explaining your work
- Walkthrough of your code structure and implementation
- Demonstration of your API using the `/docs` endpoint
- Explanation of your model training approach and service architecture

**Important Notes:**
- Your presentation should be natural and conversational
- Do NOT read from a prepared script
- Exceeding the 10-minute limit will result in point deductions

## **Evaluation Criteria**
Your assignment will be evaluated based on:
- **Technical Implementation**: Correctness and quality of your code
- **Model Performance**: Accuracy and efficiency of your NLP model
- **API Design**: Clarity and usability of your API
- **Documentation**: Completeness and clarity of your documentation
- **Presentation**: Effectiveness of your video explanation

## **Important Constraints**
- Focus on backend API development only (no frontend required)
- Use the automatically generated `/docs` endpoint for demonstration
- Strictly adhere to the 10-minute video time limit