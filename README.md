# AI-Powered Sales Reporting Automation (Make.com + Gemini API)

An end-to-end, no-code automation pipeline that pulls sales data from Google Sheets, uses Google's Gemini AI to generate a natural-language business report, and automatically distributes it via **Gmail (HTML report)** and **Slack (formatted summary)** — on a schedule, with zero manual effort.

---

## 🎯 Problem

Sales/ops teams often need a recurring weekly report summarizing revenue, profit, and category performance from a live spreadsheet. Manually pulling numbers, writing a summary, and emailing/posting it every week is repetitive and error-prone.

## 🛠️ Tools Used

- **Make.com** — visual automation/orchestration engine
- **Google Sheets API** — live sales data source
- **Google Gemini API** — LLM for generating the report narrative
- **Gmail API** — automated HTML email delivery
- **Slack API** — automated channel notifications

## 💡 Approach / Architecture

```
Google Sheets (Search Rows)
        │
        ▼
Text Aggregator (combines row data into one text block)
        │
        ▼
      Router
        ├──► HTTP → Gemini API (HTML prompt)  ──► Gmail (Send Email)
        └──► HTTP → Gemini API (Slack-markdown prompt) ──► Slack (Send Message)
```

**Key design decision:** rather than sending one AI-generated report to both channels, the workflow branches at a Router and calls the Gemini API **twice with different prompts** — one instructing the model to return clean HTML (for email rendering), and one instructing it to return plain text with Slack-style markdown (`*bold*`, `-` bullets, no HTML tags). This avoids the common mistake of dumping raw HTML into a chat client.

### Sample Gemini prompt (Slack branch)

```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "You are a professional business analyst. Generate a Weekly Sales Report summary using this data: {{aggregated_sheet_data}}\n\nRules:\n- Return ONLY plain text formatted for Slack.\n- Do NOT use any HTML tags.\n- Use *asterisks* for bold text.\n- Use a dash (-) for bullet points.\n- Keep it concise, under 200 words."
        }
      ]
    }
  ]
}
```

## 📊 Insight / Output

The automation produces a weekly business summary covering:
- Total revenue, profit, and units sold
- Category-wise performance breakdown (Electronics, Home & Furniture, Clothing & Apparel, Accessories)
- Auto-generated insights (top revenue driver, best margin category, volume leader)

**Sample finding from a test run:** Electronics drove ~43% of total revenue, but Home & Furniture delivered the highest absolute profit contribution — a classic revenue-vs-margin trade-off automatically surfaced by the AI narrative rather than manually spotted.

## 📈 Impact

- Eliminates ~30–45 minutes of manual weekly reporting work
- Removes human error in transcribing figures from the sheet
- Delivers to two channels (email + Slack) simultaneously with format tailored to each
- Fully no-code — reproducible by any analyst without writing a backend service

## ⚙️ Setup Overview

1. Connect Google Sheets module → point to your sales data range
2. Add a Text Aggregator to flatten rows into a single text block
3. Add a Router with two branches
4. In each branch, add an HTTP module (`POST` to `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent`) with header `x-goog-api-key: YOUR_API_KEY` and a JSON body containing the prompt above
5. Connect branch 1 → Gmail (Send an Email), branch 2 → Slack (Send a Message)
6. Set the scenario schedule (e.g., every Monday 9 AM)

## 🔗 Author

**Firdous Abbasi** — Data Analyst (Electrical & Electronics Engineering background)
- GitHub: [github.com/firdous-cloud-analyst](https://github.com/firdous-cloud-analyst)
- LinkedIn: [linkedin.com/in/firdous-abbasi-0080b1337](https://linkedin.com/in/firdous-abbasi-0080b1337)
