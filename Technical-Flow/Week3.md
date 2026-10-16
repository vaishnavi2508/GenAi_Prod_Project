# Knowledge Check: Answers in My Own Words

## Part 1: Technical Questions

**Q1. What is Amazon Bedrock? How is it different from the ChatGPT website?**
Bedrock is an AWS service that lets companies use AI models inside their own secure cloud. The ChatGPT website is a public app for everyone. Bedrock is private and made for business use.

**Q2. What is an LLM?**
It is a computer program that has read a huge amount of text from books and websites. Because of that, it can understand and write language like a human.

**Q3. Why `bedrock-runtime` and not `bedrock`?**
`bedrock-runtime` is for talking to the model (send a message, get a reply). `bedrock` is for managing things like listing models or settings.

**Q4. What is a system prompt? Example for a bank bot.**
It is a set of rules I give the AI before the user talks to it.
Example: `"You are a bank support bot. Only answer banking questions, and be polite."`

**Q5. System prompt vs user message?**
- System prompt = the rules. Example: *"You are an IT helper."*
- User message = the actual question. Example: *"My Wi-Fi is not working."*

**Q6. What is few-shot prompting? Example with 2 shots.**
I show the AI a couple of examples first, so it copies the pattern.

```
Ticket: "Printer broken"      -> Priority: Low
Ticket: "Server down"         -> Priority: High
Ticket: "Email login failed"  -> Priority: ?
```

**Q7. What is zero-shot? How is it different?**
Zero-shot means I ask directly with no examples. Few-shot means I give examples first.

**Q8. What instruction made Claude return only JSON?**
`"Return only valid JSON. No extra text, no markdown backticks."`

**Q9. What is `max_tokens`? What happens if it is 10?**
It sets the maximum length of the reply. At 10 tokens the answer gets cut off in the middle. (A token is a small piece of a word, not exactly one word.)

**Q10. What is hallucination?**
It is when the AI makes up fake information and says it with confidence.
Example: it invented a company policy number that does not exist.

**Q11. temperature 0 vs 1?**
- `0` = safe, predictable, same answer each time. Use it for code, data, and JSON.
- `1` = more creative and varied. Use it for brainstorming and writing.

**Q12. What did the ML model do and what did the LLM do?**
- ML model (XGBoost): quickly predicts the priority from the ticket's features.
- LLM (Claude): reads the full ticket, understands it, and writes the explanation and action plan.

**Q13. What is `json.loads()`?**
It converts JSON text into a Python dictionary. After that I can read values easily, like `data["category"]`.

**Q14. What is `anthropic_version`?**
It tells AWS which request format I am using. It is required, and for Bedrock the value is `"bedrock-2023-05-31"`.

**Q15. What does the model ID `anthropic.claude-3-sonnet-20240229-v1:0` mean?**
- `anthropic` = the company that made it
- `claude-3-sonnet` = the model name and size
- `20240229` = the model's version date (29 Feb 2024)
- `v1:0` = the ID version

---

## Part 2: Scenario-Based Questions

**Q16. System prompt says "Only IT questions". User asks "What is 2 + 2".**
Claude will politely refuse. The system prompt tells it to stay on IT topics only.

**Q17. JSON output has extra text, so `json.loads()` fails.**
Claude added chat text around the JSON. Fix the prompt:
`"Output ONLY raw JSON. No intro, no explanation, no backticks."`

**Q18. Auto-replying to IT tickets with no human check. Risks?**
- Risk 1: it may give wrong steps that break something.
- Risk 2: it may hallucinate fake facts or policies.
- Better plan: the AI writes a draft, and a human approves it before sending.

**Q19. Claude made up a fake policy. Why?**
An LLM predicts the most likely next words. When it does not know the answer, it still writes something that sounds right. This proves we cannot trust it blindly.

**Q20. "We already have XGBoost. Why do we need Claude?"**
XGBoost only gives a priority label. Claude reads the ticket, understands the problem, explains it, and writes the next steps.

**Q21. How do I reduce Claude's cost?**
1. Use a cheaper model, like Haiku.
2. Cache answers to common questions.
3. Keep prompts short and set a lower `max_tokens`.

**Q22. Why did quality drop with a 5-word system prompt?**
The AI lost its detailed rules and context, so the answers became generic.

**Q23. Why Bedrock over ChatGPT for business?**
1. Better data privacy. Company data is not used to train the models.
2. Enterprise-level AWS security and access control.
3. Easy to connect with other AWS services.

**Q24. Claude always says High priority. What is wrong?**
My examples in the prompt are probably unbalanced. Fix it by adding a mix of Low, Medium, and High examples.

**Q25. Why few-shot instead of only defining categories?**
Examples show the exact style and format I want. A definition alone can be understood in different ways.

**Q26. Category comes back as `null`. Why, and how do I handle it?**
- Reason 1: the ticket was too vague.
- Reason 2: the model missed it or ignored the format.
- Handle it in Python with a fallback:

```python
category = data.get("category") or "General"
```

**Q27. How do I make Claude reply in exactly 3 sentences?**
`"Your response must be exactly 3 sentences. No more, no less."`

**Q28. How do I stop Claude from answering a resignation ticket?**
Add a rule in the system prompt:
`"If the request is about HR or resignation, reply only: 'I only handle IT support requests.'"`

**Q29. ML says High, Claude says Critical. Which do I trust?**
I do not blindly trust either one.
- Claude understands context, like an urgent meeting.
- ML is consistent and can be measured and tested.

So I use Claude's answer as a smart suggestion, and for important tickets a human makes the final call.

**Q30. AI vs ML vs LLM, using my project?**
- **AI** = the big idea of making computers act smart. Example: my whole ticket helper.
- **ML** = computers learning from data. Example: the XGBoost model that predicts priority.
- **LLM** = a type of AI trained on lots of text. Example: Claude reading tickets and writing replies.v