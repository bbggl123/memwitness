# Privacy Redlines

> These categories must never be stored in memory, regardless of context. This is not a suggestion — it's a hard constraint on what the memory system can record.

---

## The Absolute Exclusions

**Never write** the following, even if the user explicitly shares them:

| Category | Examples |
|---|---|
| **Race / Ethnicity / Nationality** | "I'm Chinese", "My family is from Vietnam" |
| **Religion** | "I'm Buddhist", "We go to church on Sundays" |
| **Health Data** | "I have diabetes", "My doctor said..." |
| **Mental Health** | "I'm depressed", "I have anxiety", "My therapist..." |
| **Psychological Diagnoses** | "I'm bipolar", "I have ADHD" |
| **Political Beliefs** | "I support [party/policy]", "I voted for..." |
| **Sexual Orientation** | "I'm gay/straight/bi..." |
| **Gender Identity** | "I identify as..." |
| **Personal Finances** | "I earn $X", "My credit score is..." |
| **Government IDs** | Passport numbers, social security numbers, ID card numbers |
| **Credit/Financial Details** | Credit card numbers, bank account numbers |
| **Home Address** | "I live at 123 Main St" |
| **Personal Phone Number** | Personal (not work) phone numbers |
| **Criminal History** | "I was arrested for...", "I have a record for..." |
| **Abuse Experiences** | Any description of physical, emotional, or sexual abuse |
| **Psychological Test Results** | MBTI type, Enneagram type, DISC profile, IQ scores |

---

## The Test

> **If a colleague saw this line in the memory settings page, would the user be uncomfortable?**

If the answer is **yes**, don't write it. Period.

---

## Partial Write Rule

If a sentence is partially sensitive, write only the non-sensitive part. Omit the sensitive part entirely. Don't leave a placeholder.

**Example**:

User says: "I'm from Beijing, and I have a medical condition that makes me take this medication."

**Correct write**:
```
- [stated] Is from Beijing
```
(The medical condition part is omitted entirely — no placeholder, no "[redacted]")

**Incorrect write**:
```
- [stated] Is from Beijing
- [stated] Has a medical condition [REDACTED]
```

---

## Why This Exists

1. **Trust**: The user must trust that the Agent will not expose sensitive information, even accidentally
2. **Regulatory**: GDPR, CCPA, and other privacy regulations require careful handling of sensitive data
3. **Safety**: Storing sensitive data increases the blast radius if the system is compromised
4. **Quality**: Sensitive data is also the most prone to misinterpretation — removing it improves memory quality

---

## What About Work-Related Sensitive Info?

The redlines apply to **personal** sensitive data. Work-related information that the user explicitly wants stored (e.g., "my team uses Kubernetes") is fine.

**Boundary rule**: If the user says "remember this for work", it's a professional context and can be stored. If the context is personal, apply the redlines.

---

## Enforcement

The memory system enforces privacy at the **write** layer. There is no "delete this entry" feature for sensitive data — it should never be written in the first place.

**If sensitive data was accidentally written**:
1. Remove the entry from all memory files
2. Do not mention the removal to the user (it draws attention to the data)
3. Note the incident in the self-improvement log as an error pattern
4. Never store sensitive data from that interaction forward
