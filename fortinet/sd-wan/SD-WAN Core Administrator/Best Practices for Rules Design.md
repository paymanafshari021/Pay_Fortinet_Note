# Best Practices for Rules Design
---

### 1. **Look at your links and costs first**

- Every network connection (like internet lines or VPNs) has a **cost** and **availability**.
- **Cost** could be money, bandwidth, or latency.
- **Availability** means how reliable the connection is.

**Example:**

- Link A: Fiber connection, very fast, expensive.
- Link B: 4G backup line, slower, cheaper.

Before creating rules, you need to know what links you have and their pros/cons.

---

### 2. **Check what your applications need**

- Some apps are **time-sensitive**, like video calls or VoIP (voice over IP).
- Others are less critical, like downloading updates.

**Example:**

- Zoom calls → need fast, low-delay link.
- Email backup → can tolerate slower connections.

---

### 3. **Define a rule strategy**

- Rules decide which traffic goes over which link.
- **Most restrictive rules first:** Rules that are very specific should be at the top.

**Example:**

1. Rule for Zoom calls → go over Fiber (top priority).
2. Rule for email backup → go over 4G (less important).

- Use **Lowest cost** or **Best quality** for important, time-sensitive apps.
- Use **Manual strategy** (choose a link yourself) for less important apps.

---

### 4. **Review and adjust regularly**

- Network conditions change, and your rules may need updating.
- Use the **Hit Count** feature to see which rules are actually being used.
- Adjust rules based on real usage and changing requirements.

**Example:**

- If your Zoom calls are often hitting the Fiber link, the rule is working.
- If some rules are never used, you might remove or change them.

---

### ✅ Key Takeaways

- Know your links: speed, cost, reliability.
- Understand your applications: which ones need fast, reliable connections.
- Create rules: put important traffic first, use appropriate strategies.
- Monitor and adjust: networks change, so rules should too.
