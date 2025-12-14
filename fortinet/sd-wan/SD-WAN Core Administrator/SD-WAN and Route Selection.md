# SD-WAN and Route Selection
### Key Concepts Explained in Simple Words

1. **SD-WAN Rules Pick the Path, But Routing Table Must Allow It**  
   - **What it means**: SD-WAN decides *which internet link* (like a cable or fiber line) to use for your traffic. But FortiGate (the firewall) can only send the traffic if it already knows *how to reach the destination* through that link—like having a map.  
   - **Example**: You want to send a video call to Google. SD-WAN says “use the fast fiber link.” But if the routing table only knows how to reach Google via the slow cable link, it **can’t** use the fiber link—even if SD-WAN wants to.

2. **Best Match + Backup Routes = Full SD-WAN Power**  
   - **What it means**: To use SD-WAN smartly, your routing table should have:  
     - A **main route** (best match)  
     - **Backup routes** (alternatives)  
   - This lets SD-WAN freely choose any good link.  
   - **Example**:  
     - Main route: Google → Fiber link (fast)  
     - Backup route: Google → Cable link (slower)  
     → SD-WAN can pick fiber **or** switch to cable if fiber is slow.

3. **By Default: SD-WAN Can Ignore “Best” Route**  
   - **What it means**: Normally, SD-WAN doesn’t care which route is “best” in the routing table. It just picks the link based on speed, cost, or rules—even if another route is technically better.  
   - **Example**:  
     - Routing table says: Google → Fiber (best, specific route)  
     - But there’s also a general default route via Cable  
     → SD-WAN can still force traffic through Cable if rules say so (e.g., for cost saving).

4. **Optional: Force SD-WAN to Use Only “Best” Routes**  
   - **What it means**: You can turn on a setting (`fib-best-match`) so SD-WAN **only** uses links that have the *best known route* to the destination.  
   - **Example**:  
     - With `fib-best-match` ON:  
       → SD-WAN will **only** use the Fiber link for Google (because it’s the best route).  
     - Without it (default):  
       → SD-WAN can use Cable even if Fiber is better.

---

### Real-Life Analogy
Think of **driving to a friend’s house**:
- **Routing table** = Your GPS map (shows possible roads).
- **SD-WAN rules** = Your preference (“Take the highway if it’s fast, even if GPS says local road is shorter”).
- **Default behavior**: You might take the highway even if GPS says local road is “best.”
- **`fib-best-match` = ON**: You **only** take roads that GPS says are best—no shortcuts allowed.

---

### Summary Table

| Concept                                      | Default Behavior                  | With `fib-best-match`           |
| -------------------------------------------- | --------------------------------- | ------------------------------- |
| Can SD-WAN pick any link with a valid route? | Yes                               | Only if it’s the **best** route |
| Example                                      | Use Cable even if Fiber is better | Only use Fiber if it’s the best |
