# Gemini API Reproduction Results
- Run date and time: 2026-11-09T13:59:44+05:30

- Model identifier: `gemini-3.5-flash-lite`

- Temperature: Not explicitly set; model default used.

- SDK: google-genai

- Each prompt was sent as an independent API request.

- These results are new API runs, separate from AI Studio results.

## Experiment 1 - Zero-shot
### Actual prompt
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

### Raw API response (unchanged)
```text

R1: Positive

R2: Negative

R3: Neutral

R4: Positive

R5: Negative

R6: Neutral

R7: Positive

R8: Negative

R9: Neutral

R10: Positive

```

## Experiment 1 - Few-shot
### Actual prompt
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

### Raw API response (unchanged)
```text

R1: Positive

R2: Negative

R3: Neutral

R4: Positive

R5: Negative

R6: Neutral

R7: Positive

R8: Negative

R9: Neutral

R10: Positive

```

## Experiment 2 - Direct
### Actual prompt
```text

A shopkeeper purchases 15 notebooks at ₹40 each and 10 pens at ₹12 each. He sells 12 notebooks at ₹55 each and the remaining notebooks at ₹35 each. He sells all 10 pens at ₹15 each.

Calculate the total cost price, total selling price, profit or loss, and profit or loss percentage. Round the percentage to two decimal places. Return the final results without a step-by-step explanation.

```

### Raw API response (unchanged)
```text

* **Total Cost Price:** ₹720

* **Total Selling Price:** ₹870

* **Profit or Loss:** Profit of ₹150

* **Profit or Loss Percentage:** 20.83%

```

## Experiment 2 - Step-by-step
### Actual prompt
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

### Raw API response (unchanged)
```text

Here is the step-by-step breakdown of the calculations:

**1. Total Cost Price:**

* Cost of 15 notebooks = $15 \times ₹40 = ₹600$

* Cost of 10 pens = $10 \times ₹12 = ₹120$

* **Total Cost Price (CP)** = $₹600 + ₹120 = ₹720$

**2. Selling Price of the Notebooks:**

* 12 notebooks sold at ₹55 each = $12 \times ₹55 = ₹660$

* Remaining notebooks = $15 - 12 = 3$ notebooks

* 3 notebooks sold at ₹35 each = $3 \times ₹35 = ₹105$

* **Total SP of notebooks** = $₹660 + ₹105 = ₹765$

**3. Selling Price of the Pens:**

* 10 pens sold at ₹15 each = $10 \times ₹15 = ₹150$

* **Total SP of pens** = $₹150$

**4. Total Selling Price:**

* **Total Selling Price (SP)** = $₹765 \text{ (notebooks)} + ₹150 \text{ (pens)} = ₹915$

**5. Profit or Loss and Percentage:**

* Since Total SP ($₹915$) is greater than Total CP ($₹720$), the shopkeeper makes a profit.

* **Profit** = $\text{Total SP} - \text{Total CP} = ₹915 - ₹720 = ₹195$

* **Profit Percentage** = $\left(\frac{\text{Profit}}{\text{Total CP}}\right) \times 100 = \left(\frac{195}{720}\right) \times 100 \approx 27.0833...\%$

---

### **Calculation Summary:**

1. **Total Cost Price:** ₹720

2. **Selling Price of Notebooks:** ₹765

3. **Selling Price of Pens:** ₹150

4. **Total Selling Price:** ₹915

5. **Profit / Loss:** Profit of ₹195 (27.08%)

### **Final Answer:**

The shopkeeper makes a **profit of ₹195**, which is a **27.08%** profit.

```

## Experiment 3 - Basic JSON
### Actual prompt
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

### Raw API response (unchanged)
```text

[

  {

    "merchant_name": "Sharma Electronics",

    "transaction_date": "2026-11-03",

    "amount_inr": 850

  },

  {

    "merchant_name": "City Book House",

    "transaction_date": "2026-10-28",

    "amount_inr": 1299

  },

  {

    "merchant_name": "Green Mart",

    "transaction_date": null,

    "amount_inr": 450

  },

  {

    "merchant_name": "Metro Shoes",

    "transaction_date": "2026-11-02",

    "amount_inr": null

  },

  {

    "merchant_name": "Lotus Cafe",

    "transaction_date": "2026-11-01",

    "amount_inr": 275.50

  }

]

```

- JSON evaluation: ****PASS****

- Details: Valid JSON. All five records, required keys, data types and 15 field values match.

## Experiment 3 - Explicit Schema Prompt
### Actual prompt
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

4. Amounts must be numeric, without currency symbols or commas.

5. Do not add keys.

6. Return only JSON, without Markdown fences or explanations.

Example:

Input: Paid ₹500 to ABC Store on 5 November 2026.

Output: {"merchant_name":"ABC Store",

"transaction_date":"2026-11-05","amount_inr":500}

Transactions:

T1: Paid ₹850 to Sharma Electronics on 3 November 2026 for a keyboard.

T2: On 2026-10-28, City Book House charged me Rs. 1,299 for textbooks.

T3: I paid ₹450 to Green Mart, but the receipt does not show the date.

T4: Receipt from Metro Shoes dated 2 November 2026; total amount is missing.

T5: Lotus Cafe receipt: dated 1 Nov 2026; total ₹275.50.

```

### Raw API response (unchanged)
```text

[

  {

    "merchant_name": "Sharma Electronics",

    "transaction_date": "2026-11-03",

    "amount_inr": 850

  },

  {

    "merchant_name": "City Book House",

    "transaction_date": "2026-10-28",

    "amount_inr": 1299

  },

  {

    "merchant_name": "Green Mart",

    "transaction_date": null,

    "amount_inr": 450

  },

  {

    "merchant_name": "Metro Shoes",

    "transaction_date": "2026-11-02",

    "amount_inr": null

  },

  {

    "merchant_name": "Lotus Cafe",

    "transaction_date": "2026-11-01",

    "amount_inr": 275.50

  }

]

```

- JSON evaluation: ****PASS****

- Details: Valid JSON. All five records, required keys, data types and 15 field values match.

## Experiment 3 - API Enforced JSON Schema
### Actual prompt
```text

Extract merchant_name, transaction_date, and amount_inr from each transaction. Return one object per transaction in the same order. Use null when a field is missing. Dates must use YYYY-MM-DD. Amounts must be JSON numbers in INR.

Transactions:

T1: Paid ₹850 to Sharma Electronics on 3 November 2026 for a keyboard.

T2: On 2026-10-28, City Book House charged me Rs. 1,299 for textbooks.

T3: I paid ₹450 to Green Mart, but the receipt does not show the date.

T4: Receipt from Metro Shoes dated 2 November 2026; total amount is missing.

T5: Lotus Cafe receipt: dated 1 Nov 2026; total ₹275.50.

```

### Raw API response (unchanged)
```text

[

  {

    "merchant_name": "Sharma Electronics",

    "transaction_date": "2026-11-03",

    "amount_inr": 850

  },

  {

    "merchant_name": "City Book House",

    "transaction_date": "2026-10-28",

    "amount_inr": 1299

  },

  {

    "merchant_name": "Green Mart",

    "transaction_date": null,

    "amount_inr": 450

  },

  {

    "merchant_name": "Metro Shoes",

    "transaction_date": "2026-11-02",

    "amount_inr": null

  },

  {

    "merchant_name": "Lotus Cafe",

    "transaction_date": "2026-11-01",

    "amount_inr": 275.50

  }

]

```

- JSON evaluation: ****PASS****

- Details: Valid JSON. All five records, required keys, data types and 15 field values match.
