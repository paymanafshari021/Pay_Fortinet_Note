✅ **Here is the exact STEP-BY-STEP guide** to create professional firewall documentation for your customers using **Sophos Firewall** (works for ALL Sophos Firewalls – XGS, XG, etc.).

This is the **official and best method** in 2026 (Sophos Firewall Configuration Viewer).  
It creates a clean, searchable HTML document that your customers love.  
You can do this alone or together with your old partner – just share the screen or send the files.

### Step 1: Log in to the Sophos Firewall
1. Open the web browser.
2. Go to the firewall IP (example: `https://192.168.1.1` or the public address).
3. Log in with admin username + password.

### Step 2: Export the Configuration (Full Documentation)
1. In the left menu, click **Backup & firmware**.
2. Click **Import / Export**.
3. Choose **Full configuration** (this is best for complete customer documentation).
   - (If you want only specific parts like Firewall Rules or VPN, choose **Selective configuration** and tick what you need.)
4. Click **Export** / **Download**.
5. Save the file on your computer (it is usually called something like `config.tar` or `Entities.tar`).

### Step 3: Unpack the File and Get Entities.xml
1. Right-click the downloaded file → **Extract** (use 7-Zip or WinRAR – free).
2. Inside the folder you will see a file called **Entities.xml**.
3. Copy or remember where this `Entities.xml` file is saved.

### Step 4: Create the Nice Readable Documentation
1. Open this official link in a new browser tab:  
   **https://docs.sophos.com/nsg/sophos-firewall/config-viewer/index.html**
2. Click the big button **Upload configuration** (or similar).
3. Select the **Entities.xml** file you just unpacked.
4. Wait a few seconds – the tool will show a clean, beautiful report of the **whole firewall**:
   - Firewall Rules
   - NAT
   - VPN
   - Interfaces
   - Users
   - IPS / Web Protection
   - Everything!

### Step 5: Save the Documentation for the Customer
1. In the top right corner, click **Export HTML**.
2. Save the file with a good name, for example:  
   `CustomerName-Firewall-Documentation-2026-03-29.html`
3. (Optional but recommended) Create a simple 1-page cover PDF in Word:
   - Customer name
   - Firewall model + serial number + firmware version
   - Date
   - Your company name + contact
   - Text: “This is the complete configuration of your Sophos Firewall as of today.”

### Step 6: Repeat for ALL Firewalls (Whole Sophos Firewalls)
Do exactly the same steps (1–5) for **every Sophos Firewall** your customer has.  
Just change the file name each time:
- `CustomerName-FW-Office-2026-03-29.html`
- `CustomerName-FW-Branch1-2026-03-29.html`
etc.

### How to Deliver to the Customer
- Send the HTML file(s) + cover PDF by email.
- Or put everything in one folder and zip it.
- Customers can open the HTML file in any browser – no login needed.

**Tip for you and your old partner:**  
You can also compare two exports (before and after changes) in the same viewer – very useful if you want to show what was changed.

Would you like:
- A ready-made **cover page text template** (copy-paste in Word)?
- Instructions how to do **only Firewall Rules** (if customer wants it shorter)?
- Or steps if the firewall is managed in **Sophos Central**?

Just tell me and I give you the next part immediately!  
This method works 100% and is what Sophos recommends for customer documentation.