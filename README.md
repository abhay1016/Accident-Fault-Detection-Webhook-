# Accident-Fault-Detection-Webhook-
# 🚦 Accident Fault Detection Webhook (n8n Automation)

This project uses an [n8n](https://n8n.io) workflow to automatically determine fault from accident reports based on keywords in the user's narrative. It accepts POST requests at a webhook, analyzes the narrative, and emails the outcome.

---

## 📌 Project Overview

- Accepts accident report data via *POST request* to an n8n *Webhook*.
- Parses the name, location, and narrative from the payload.
- Analyzes the narrative for keywords to detect whether the other party may be at fault.
- Sends an email with the outcome using the *Gmail node*.

---

## 🔁 Workflow Steps

1. *Webhook Node*  
   - Endpoint: POST  https://abh123.app.n8n.cloud/webhook-test/0c267d0a-4013-4f01-8676-773d8c978029

   - Accepts JSON payload:
     json
     {
       "name": "John Doe",
       "location": "Newark, NJ",
       "narrative": "The other driver ran a red light and hit me on the side."
     }
     

2. *Function (Code) Node*  
   - Extracts data from body using:
     javascript
     const body = $input.first().json.body;
     const { name, narrative } = body;

     const faultIndicators = ["hit me", "ran a red light", "not my fault"];
     const lowerNarrative = narrative.toLowerCase();

     let likelyAtFault = false;

     for (const keyword of faultIndicators) {
       if (lowerNarrative.includes(keyword)) {
         likelyAtFault = true;
         break;
       }
     }

     const message = likelyAtFault
       ? `${name} is likely NOT at fault based on the narrative.`
       : `${name}'s fault status is unclear from the narrative.`;

     return [{ json: { message } }];
     

3. *Gmail Node*  
   - Sends an email with:
     - *Subject:* Fault or Not Fault
     - *Body:* {{ $json.message }}

---

## 🛠 How to Test

### python:import requests

url = "https://abh123.app.n8n.cloud/webhook-test/0c267d0a-4013-4f01-8676-773d8c978029"

data = {
    "name": "John Doe",
    "location": "Newark, NJ",
    "narrative": "The other driver ran a red light and hit me on the side."
}

response = requests.post(url, json=data)
print(response.text)
