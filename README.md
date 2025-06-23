#  Universal Medical JSON Conversion Agent

This project is an AI-powered system designed to **convert raw JSON data from FDA-approved RPM devices** into a **standardized format**, using **Google’s Agent Development Kit (ADK)** for multi-agent orchestration and **Cleanlab’s Trustworthy Language Model (TLM)** for validating output quality.

It handles:
- Known and unknown device formats
- Web scraping + document retrieval
- LLM-based conversion with fallback and trust scoring

---

##  Tech Stack

| Purpose            | Technology                              |
|--------------------|------------------------------------------|
| Agent Framework    | [Google ADK](https://github.com/google-deepmind/adk) |
| LLM Trust Scoring  | [Cleanlab TLM](https://help.cleanlab.ai/tlm/tutorials/tlm/) |
| Programming Lang   | Python                                  |
| Web Framework      | FastAPI                                 |
| Database           | MongoDB                                 |
| Queue (optional)   | Redis / Celery                          |
| Vector DB          | FAISS or Chroma                         |
| Web Search         | Brave API                               |
| Scraping           | Playwright or BeautifulSoup             |
| LLMs               | OpenAI GPT-4o / Claude 3                |

---

##  How It Works (Workflow)

1. **Raw JSON submitted** (via webhook or API)
2. `DeviceContextAgent` checks if the device is known (cache/vector DB)
3. If known → convert using saved mappings
4. If unknown:
   - Use `DocFetcherAgent` to scrape device documentation (HTML or PDF)
   - Use `RAGAgent` to extract relevant field mappings
5. `JSONConverterAgent` converts the raw payload to standard format
6. `TLMValidationAgent` scores the reliability of the result using Cleanlab TLM
7. Result is saved to MongoDB with a trust score
8. Return success/failure + confidence score

---

##  AI Agents in ADK

### 1. `DeviceContextAgent`
- Checks known device formats from FAISS or MongoDB
- Returns mapping schema or flags as unknown

### 2. `DocFetcherAgent`
- Uses Brave Search API to find documentation online
- Scrapes content for mapping info using Playwright

### 3. `RAGAgent`
- Converts scraped docs into embeddings
- Uses FAISS or Chroma to retrieve relevant info for conversion

### 4. `JSONConverterAgent`
- Maps raw fields to a standardized JSON output format

### 5. `TLMValidationAgent`
- Uses Cleanlab's TLM to:
  - Detect hallucinations or invalid output
  - Score confidence on a scale of 0–1
  - Suggest retry if score is too low

---

##  Sample Output

```json
{
  "DeviceId": "OM123456",
  "MeasurementType": "Blood Pressure",
  "Values": [
    { "Type": "systolic", "Value": 130 },
    { "Type": "diastolic", "Value": 80 },
    { "Type": "pulse", "Value": 72 }
  ],
  "MeasuredAt": "2025-06-17T12:30:00Z",
  "PatientId": "patient-001",
  "ConfidenceScore": 0.92
}
```

## Development Checklist

- [ ] Set up Google ADK agent runtime  
- [ ] Create `/convert` API endpoint  
- [ ] Implement `DeviceContextAgent`  
- [ ] Add Brave API search + Playwright scraping  
- [ ] Implement `RAGAgent` with FAISS  
- [ ] Implement `JSONConverterAgent`  
- [ ] Integrate Cleanlab’s TLM for validation  
- [ ] Save converted JSON + trust score to MongoDB  
- [ ] Log failures + low-confidence results  

---

## Future Features (Optional)

- [ ] Admin UI to review failed conversions  
- [ ] Metrics dashboard (conversion rate, device types)  
- [ ] Batch uploader for large datasets  
- [ ] Add multilingual voice reminder system (Phase 2)  

---

##  Goal

Build a reliable, intelligent agent system that can:

-  Normalize diverse device data automatically  
-  Search and learn from external sources (like device manuals)  
-  Use LLMs with confidence scoring for safer, more reliable AI integration  
