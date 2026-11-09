# Prompt Engineering Experiments

## 1. Introduction

For this assignment, I tested three prompt-engineering techniques: zero-shot and few-shot prompting, step-by-step prompting for a multi-part calculation, and structured JSON output. I first ran the prompts in separate Google AI Studio chats and saved the responses. After that, I reproduced the same experiments through the Gemini API and added a test using API-enforced JSON Schema.

The purpose was to compare the outputs, check whether they followed the requested format, and see where the prompting techniques helped or made no measurable difference. I have included the outputs as they were returned. I have not changed an incorrect answer into a correct one in the recorded results; corrections are shown separately for evaluation.

## 2. Experimental setup

### Google AI Studio experiments

- Model used: Gemini 3.8 Flash
- Each prompt was run in a separate new chat.
- The zero-shot and few-shot versions used the same ten review inputs.
- The direct and step-by-step versions used the same maths problem.
- The two JSON prompts used the same five transaction inputs.
- The exact temperature and other generation settings were not recorded at the time of the runs.

### API reproduction

The first API attempt using Gemini 3.8 Flash returned HTTP 503 with the message that the model was experiencing high demand. I then changed the API reproduction model to Gemini 3.5 Flash-Lite. The API requests completed successfully with that model.

- Model used for API reproduction: `gemini-3.5-flash-lite`
- SDK: `google-genai`
- Runtime: Python 3.13.1
- Each prompt was sent as an independent API request.
- Temperature was not explicitly set; the model default was used.
- Seven API requests completed, with zero request errors.
- The [API reproduction log](api_results/gemini_api_results_20261109_135953.md) contains the exact prompts and raw responses returned by the API for all seven requests.

The AI Studio and API results must not be treated as a same-model comparison: the AI Studio runs used Gemini 3.8 Flash, while the successful API runs used Gemini 3.5 Flash-Lite. This model change is stated here so the results are not misleading.

---

## 3. Experiment 1 — Zero-shot vs. few-shot sentiment classification

### Objective

The task was to classify ten customer reviews as Positive, Negative, or Neutral. The same ten inputs were used for both approaches so that the results could be compared directly.

### Test dataset and expected labels

| ID | Customer review | Expected label |
|---|---|---|
| R1 | The camera takes sharp photos and focuses quickly. | Positive |
| R2 | The app crashes every time I open it. | Negative |
| R3 | The parcel was delivered on Tuesday. | Neutral |
| R4 | Battery lasts all day and charging is quick. | Positive |
| R5 | The keyboard keys stopped responding after a week. | Negative |
| R6 | The box contains the device, cable, and manual. | Neutral |
| R7 | Customer support solved my issue within minutes. | Positive |
| R8 | The headphones disconnect every few minutes. | Negative |
| R9 | The order number is printed on the receipt. | Neutral |
| R10 | The material feels sturdy and looks premium. | Positive |

### Prompt 1 — Zero-shot

```text
You are a customer sentiment classification system.

Classify each customer review into exactly one of these categories:
- Positive
- Negative
- Neutral

Rules:
1. Classify each review independently.
2. Consider the overall sentiment expressed.
3. Return exactly one label per review.
4. Do not provide explanations or additional text.
5. Follow this output format: ID: Label

Reviews:
R1: The camera takes sharp photos and focuses quickly.
R2: The app crashes every time I open it.
R3: The parcel was delivered on Tuesday.
R4: Battery lasts all day and charging is quick.
R5: The keyboard keys stopped responding after a week.
R6: The box contains the device, cable, and manual.
R7: Customer support solved my issue within minutes.
R8: The headphones disconnect every few minutes.
R9: The order number is printed on the receipt.
R10: The material feels sturdy and looks premium.
```

### Raw Google AI Studio output — zero-shot

```text
R1: Positive R2: Negative R3: Neutral R4: Positive R5: Negative R6: Neutral R7:
Positive R8: Negative R9: Neutral R10: Positive
```

### Prompt 2 — Few-shot

Four labelled examples were added before the ten test reviews.

```text
You are a customer sentiment classification system.

Classify each customer review into exactly one category:
Positive, Negative, or Neutral.

Follow the classification pattern demonstrated below.

Examples:

Input: The battery lasts all day and setup took two minutes.
Output: Positive

Input: The screen froze repeatedly and support never replied.
Output: Negative

Input: The package includes a cable, charger, and instruction booklet.
Output: Neutral

Input: The build feels solid and the buttons work smoothly.
Output: Positive

Now classify these new reviews.

Rules:
1. Classify every review independently.
2. Use only Positive, Negative, or Neutral.
3. Do not explain your answers.
4. Return exactly one label per review using ID: Label.

Reviews:
R1: The camera takes sharp photos and focuses quickly.
R2: The app crashes every time I open it.
R3: The parcel was delivered on Tuesday.
R4: Battery lasts all day and charging is quick.
R5: The keyboard keys stopped responding after a week.
R6: The box contains the device, cable, and manual.
R7: Customer support solved my issue within minutes.
R8: The headphones disconnect every few minutes.
R9: The order number is printed on the receipt.
R10: The material feels sturdy and looks premium.
```

### Raw Google AI Studio output — few-shot

```text
R1: Positive R2: Negative R3: Neutral R4: Positive R5: Negative R6: Neutral R7:
Positive R8: Negative R9: Neutral R10: Positive
```

### Results

| Version | Correct predictions | Accuracy |
|---|---:|---:|
| Zero-shot, Gemini 3.8 Flash | 10/10 | 100% |
| Few-shot, Gemini 3.8 Flash | 10/10 | 100% |
| Zero-shot, Gemini 3.5 Flash-Lite API | 10/10 | 100% |
| Few-shot, Gemini 3.5 Flash-Lite API | 10/10 | 100% |

The API outputs for zero-shot and few-shot were also identical to the expected labels. Their exact API prompts and unedited responses are in the accompanying API reproduction log.

### Analysis

Both prompts classified all ten reviews correctly in the AI Studio run. The API reproduction gave the same result for both prompt types. The four examples in the few-shot prompt did not increase the measured accuracy on this dataset because the zero-shot prompt was already correct on every review. For these fairly direct examples, clear category definitions and output instructions were enough. This result does not show that few-shot prompting is never useful; it only shows that it did not improve accuracy on this particular set of ten reviews.

---

## 4. Experiment 2 — Direct prompt vs. step-by-step prompting

### Objective

The task was to calculate the total cost price, total selling price, profit or loss, and profit percentage for a shopkeeper selling notebooks and pens. The question requires several calculations, so it is suitable for checking whether asking for the calculation steps changes the result.

### Problem used in both prompts

A shopkeeper purchases 15 notebooks at ₹40 each and 10 pens at ₹12 each. He sells 12 notebooks at ₹55 each and the remaining notebooks at ₹35 each. He sells all 10 pens at ₹15 each.

### Prompt 3 — Direct

```text
A shopkeeper purchases 15 notebooks at ₹40 each and 10 pens at ₹12 each. He sells 12 notebooks at ₹55 each and the remaining notebooks at ₹35 each. He sells all 10 pens at ₹15 each.

Calculate the total cost price, total selling price, profit or loss, and profit or loss percentage. Round the percentage to two decimal places. Return the final results without a step-by-step explanation.
```

### Raw Google AI Studio output — direct

```text
- Total Cost Price: ₹720
- Total Selling Price: ₹915
- Profit: ₹195
- Profit Percentage: 27.08%
```

### Prompt 4 — Step-by-step

```text
A shopkeeper purchases 15 notebooks at ₹40 each and 10 pens at ₹12 each. He sells 12 notebooks at ₹55 each and the remaining notebooks at ₹35 each. He sells all 10 pens at ₹15 each.

Calculate the total cost price, total selling price, profit or loss, and profit or loss percentage.

Work through the calculations carefully. Show a concise numbered calculation summary covering:
1. Total cost price of notebooks and pens.
2. Selling price of the notebooks.
3. Selling price of the pens.
4. Total selling price.
5. Profit or loss and its percentage.

Round the percentage to two decimal places. Clearly state the final answer.
```

### Raw Google AI Studio output — step-by-step

```text
Here is the step-by-step calculation summary:

Calculation Summary

1.  Total Cost Price (CP) of notebooks and pens:

      - Cost of notebooks: 15 \times ₹40 = ₹600
      - Cost of pens: 10 \times ₹12 = ₹120
      - Total CP = ₹600 + ₹120 = ₹720

2.  Selling Price (SP) of the notebooks:

      - First 12 notebooks: 12 \times ₹55 = ₹660
      - Remaining notebooks (15 - 12 = 3): 3 \times ₹35 = ₹105
      - Total SP of notebooks = ₹660 + ₹105 = ₹765

3.  Selling Price (SP) of the pens:

      - Total SP of pens = 10 \times ₹15 = ₹150

4.  Total Selling Price:

      - Total SP = ₹765 + ₹150 = ₹915

5.  Profit or Loss and its percentage:

      - Since Total SP (₹915) is greater than Total CP (₹720), there is a Profit.
      - \text{Profit} = \text{Total SP} - \text{Total CP} = ₹915 - ₹720 = ₹195
      - \text{Profit Percentage} = \left(\frac{\text{Profit}}{\text{Total CP}}\right) \times 100 = \left(\frac{195}{720}\right) \times 100 \approx
        27.08%

Final Answer

  - Total Cost Price: ₹720
  - Total Selling Price: ₹915
  - Profit: ₹195
  - Profit Percentage: 27.08%
```

### Correct calculation for evaluation

| Calculation | Working | Correct result |
|---|---|---:|
| Cost of notebooks | 15 × ₹40 | ₹600 |
| Cost of pens | 10 × ₹12 | ₹120 |
| Total cost price | ₹600 + ₹120 | ₹720 |
| Selling price of notebooks | (12 × ₹55) + (3 × ₹35) | ₹765 |
| Selling price of pens | 10 × ₹15 | ₹150 |
| Total selling price | ₹765 + ₹150 | ₹915 |
| Profit | ₹915 − ₹720 | ₹195 |
| Profit percentage | (₹195 / ₹720) × 100 | 27.08% |

### API reproduction results — Gemini 3.5 Flash-Lite

The direct API prompt returned this raw response:

```text
* **Total Cost Price:** ₹720
* **Total Selling Price:** ₹870
* **Profit or Loss:** Profit of ₹150
* **Profit or Loss Percentage:** 20.83%
```

This answer is incorrect. The cost price is correct, but the selling price, profit, and percentage do not match the calculation above.

The step-by-step API prompt returned a correct calculation. Its raw response is preserved in `api_results/gemini_api_results_20261109_135953.md` and includes the notebook selling price of ₹765, pen selling price of ₹150, total selling price of ₹915, profit of ₹195, and profit percentage of approximately 27.08%.

### Results comparison

| Run | Direct prompt | Step-by-step prompt |
|---|---|---|
| AI Studio — Gemini 3.8 Flash | Correct | Correct |
| API — Gemini 3.5 Flash-Lite | Incorrect | Correct |

### Analysis

In Google AI Studio, both versions produced the correct final answer, so the step-by-step instruction did not improve accuracy in that run. In the API reproduction with Gemini 3.5 Flash-Lite, the direct answer contained an arithmetic error, while the step-by-step response showed the intermediate calculations and reached the correct result. This made the step-by-step answer easier to check and it was correct in that API run. The test uses one maths problem, so it is not enough to conclude that step-by-step prompting will always improve accuracy. The difference between the two model runs is also important when interpreting the results.

---

## 5. Experiment 3 — Structured JSON output

### Objective

The task was to extract three fields from five informal transaction descriptions:

- `merchant_name`: string or null
- `transaction_date`: date in `YYYY-MM-DD` format, or null
- `amount_inr`: number or null

Missing information had to be returned as `null` rather than guessed.

### Test inputs and expected values

| ID | Input | Expected merchant | Expected date | Expected amount |
|---|---|---|---|---:|
| T1 | Paid ₹850 to Sharma Electronics on 3 November 2026 for a keyboard. | Sharma Electronics | 2026-11-03 | 850 |
| T2 | On 2026-10-28, City Book House charged me Rs. 1,299 for textbooks. | City Book House | 2026-10-28 | 1299 |
| T3 | I paid ₹450 to Green Mart, but the receipt does not show the date. | Green Mart | null | 450 |
| T4 | Receipt from Metro Shoes dated 2 November 2026; total amount is missing. | Metro Shoes | 2026-11-02 | null |
| T5 | Lotus Cafe receipt: dated 1 Nov 2026; total ₹275.50. | Lotus Cafe | 2026-11-01 | 275.50 |

### Prompt 5 — Basic JSON extraction

```text
Extract merchant name, transaction date, and amount in INR from each transaction below.

Return only a valid JSON array containing one object per transaction.
Use the keys merchant_name, transaction_date, and amount_inr.

If information is unavailable, use null.
Dates should use YYYY-MM-DD.
Amounts must be numbers without currency symbols.
Do not add Markdown or explanations.

Transactions:
T1: Paid ₹850 to Sharma Electronics on 3 November 2026 for a keyboard.
T2: On 2026-10-28, City Book House charged me Rs. 1,299 for textbooks.
T3: I paid ₹450 to Green Mart, but the receipt does not show the date.
T4: Receipt from Metro Shoes dated 2 November 2026; total amount is missing.
T5: Lotus Cafe receipt: dated 1 Nov 2026; total ₹275.50.
```

### Raw Google AI Studio output — basic JSON

```json
[ { "merchant_name": "Sharma Electronics", "transaction_date": "2026-11-03",
"amount_inr": 850 }, { "merchant_name": "City Book House", "transaction_date":
"2026-10-28", "amount_inr": 1299 }, { "merchant_name": "Green Mart",
"transaction_date": null, "amount_inr": 450 }, { "merchant_name": "Metro Shoes",
"transaction_date": "2026-11-02", "amount_inr": null }, { "merchant_name":
"Lotus Cafe", "transaction_date": "2026-11-01", "amount_inr": 275.5 } ]
```

### Prompt 6 — Explicit schema in the prompt

```text
You are a financial transaction data extraction system.

Extract information from the five transaction descriptions and return a JSON array with exactly five objects, in the same order as the inputs.

Required JSON schema:
{
  "type": "array",
  "items": {
    "type": "object",
    "properties": {
      "merchant_name": {"type": ["string", "null"]},
      "transaction_date": {
        "type": ["string", "null"],
        "description": "Date in YYYY-MM-DD format"
      },
      "amount_inr": {
        "type": ["number", "null"],
        "description": "Transaction amount in Indian rupees"
      }
    },
    "required": [
      "merchant_name",
      "transaction_date",
      "amount_inr"
    ],
    "additionalProperties": false
  }
}

Rules:
1. Every object must contain all three keys.
2. Never guess missing information. Use null when unavailable.
3. Convert dates into YYYY-MM-DD format.
4. Amounts must be numeric, without currency symbols or commas. Preserve decimal values.
5. Do not add keys.
6. Return only JSON, without Markdown fences or explanations.

Example:
Input: Paid ₹500 to ABC Store on 5 November 2026.
Output: {"merchant_name":"ABC Store","transaction_date":"2026-11-05","amount_inr":500}

Transactions:
T1: Paid ₹850 to Sharma Electronics on 3 November 2026 for a keyboard.
T2: On 2026-10-28, City Book House charged me Rs. 1,299 for textbooks.
T3: I paid ₹450 to Green Mart, but the receipt does not show the date.
T4: Receipt from Metro Shoes dated 2 November 2026; total amount is missing.
T5: Lotus Cafe receipt: dated 1 Nov 2026; total ₹275.50.
```

### Raw Google AI Studio output — explicit schema prompt

The response contained the same five records and values as the basic JSON response. The complete response, as supplied from the AI Studio run, was:

```json
[ { "merchant_name": "Sharma Electronics", "transaction_date": "2026-11-03",
"amount_inr": 850 }, { "merchant_name": "City Book House", "transaction_date":
"2026-10-28", "amount_inr": 1299 }, { "merchant_name": "Green Mart",
"transaction_date": null, "amount_inr": 450 }, { "merchant_name": "Metro Shoes",
"transaction_date": "2026-11-02", "amount_inr": null }, { "merchant_name":
"Lotus Cafe", "transaction_date": "2026-11-01", "amount_inr": 275.5 } ]
```

### API structured-output test

In the API reproduction, three approaches were tested on the same five transaction inputs:

1. Basic JSON instructions in the prompt.
2. An explicit schema and extraction rules written in the prompt.
3. A JSON Schema passed separately through the Gemini API structured-output configuration.

The API reproduction log records a `PASS` for all three approaches. For each approach, the response parsed as JSON, contained five records, included all required keys, used the expected data types, and matched all 15 expected field values.

### Results

| Method | Records correct | Field values correct | Result |
|---|---:|---:|---|
| AI Studio — basic JSON prompt | 5/5 | 15/15 | Pass |
| AI Studio — explicit schema prompt | 5/5 | 15/15 | Pass |
| API — basic JSON prompt | 5/5 | 15/15 | Pass |
| API — explicit schema prompt | 5/5 | 15/15 | Pass |
| API — schema enforced by API | 5/5 | 15/15 | Pass |

### Analysis

The basic prompt already returned all five records correctly, including the two missing values represented by `null` and the decimal amount of ₹275.50. Adding the schema and a worked example did not improve the measured result on this set because the basic prompt had already achieved 5/5. The API-enforced JSON Schema test also passed all five records and confirmed the required structure at the API level. These results show that all three approaches worked on the selected inputs, but this small and relatively straightforward dataset does not establish that they would perform equally well on longer, more ambiguous transaction descriptions.

---

## 6. Overall findings

1. **Few-shot prompting:** The zero-shot and few-shot prompts both achieved 100% on the ten sentiment reviews. The examples did not increase accuracy on this dataset.
2. **Step-by-step prompting:** In the AI Studio run, both prompts were correct. In the API reproduction, the direct response made an arithmetic error while the step-by-step response was correct. The visible calculations made the latter easier to verify.
3. **Structured output:** The basic JSON prompt, explicit schema prompt, and API-enforced JSON Schema each passed all five transaction inputs. The schema-enforced API test was successfully completed.
4. **Model differences matter:** The AI Studio runs used Gemini 3.8 Flash. The successful API reproduction used Gemini 3.5 Flash-Lite after the first model returned HTTP 503. Results from the two models are reported separately.

## 7. Limitations

- The sentiment dataset contains ten short reviews, and each prompt version was run once per environment. The results do not measure repeatability over many independent runs.
- The reasoning comparison uses one multi-step word problem. More problems would be needed to draw a general conclusion about accuracy.
- The transaction dataset contains five examples. It includes missing fields and one decimal amount, but more ambiguous and varied inputs would provide a stronger test.
- The exact generation settings used in Google AI Studio were not recorded.
- The API reproduction used the model default temperature rather than a recorded temperature value.
- The API reproduction used Gemini 3.5 Flash-Lite, not Gemini 3.8 Flash. This was a deliberate change after the 3.8 Flash API request returned HTTP 503 due to high demand.

## 8. Conclusion

The experiments showed that a clear zero-shot prompt can be enough for a simple classification task, while adding examples did not change the result on the ten reviews I tested. The maths experiment showed why it is useful to ask for visible calculation steps: in the API run, the direct answer was wrong, but the step-by-step answer could be checked and was correct. For transaction extraction, all three JSON approaches worked on the five test inputs, and the API-enforced schema test confirmed that structured output could be used successfully through the API.

The main lesson from these runs is that a prompt should be evaluated with actual test inputs and checked against expected results. A response that looks convincing is not necessarily correct, and a valid JSON response still needs its values checked.

## 9. Submission contents

The public repository should contain:

```text
prompt-engineering-task2/
├── README.md
├── Prompt_Engineering_Report.md
└── api_results/
    └── gemini_api_results_20261109_135953.md
```

The API reproduction log is included as supporting evidence. It preserves the seven API prompts and raw responses, including the direct maths error and the three JSON validation passes.
