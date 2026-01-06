To turn your current single-user bookmark manager (which saves data only in the browser's `localStorage`) into a **multi-user shared bookmark system** hosted on your server, you need to add a **backend** that stores the bookmarks in a database and serves them via an API.

Here’s a simple, practical way to achieve this using **Node.js + Express + SQLite** (very lightweight, no separate database server needed). All users will then share the same bookmarks.

### Step 1: Project Structure
Create a folder on your server:

```
shared-bookmark-manager/
├── public/
│   └── index.html          # Your existing HTML file (copy it here)
├── server.js               # Backend code
├── package.json
└── bookmarks.db            # Will be created automatically (SQLite)
```

### Step 2: Copy Your HTML
Put your full HTML code into `public/index.html` (no changes needed yet, but we’ll modify it later).

```
<!DOCTYPE html>

<html lang="en">

<head>

<meta charset="UTF-8">

<title>Ultimate Bookmark Manager</title>

<style>

:root{--bg:#020617;--card:#111827;--text:#e5e7eb;--accent:#38bdf8;--input:#1f2933;--border:#334155}

.light{--bg:#f1f5f9;--card:#ffffff;--text:#0f172a;--accent:#0284c7;--input:#f8fafc;--border:#cbd5e1}

*{box-sizing:border-box}

body{margin:0;min-height:100vh;background:linear-gradient(135deg,var(--bg),#020617);font-family:system-ui,sans-serif;color:var(--text);}

.left-card{position:fixed;top:50%;left:30px;transform:translateY(-50%);width:340px;}

.right-area{margin-left:420px;padding:40px;}

.bookmark-layout{display:grid;grid-template-columns:200px 1fr;gap:20px;}

.card{background:rgba(17,24,39,.9);backdrop-filter:blur(12px);border-radius:16px;padding:20px;box-shadow:0 20px 40px rgba(0,0,0,.4);}

.light .card{background:var(--card)}

h1,h2{margin:0 0 10px}

input,button{width:100%;padding:10px;margin-top:10px;border-radius:10px;border:1px solid var(--border);background:var(--input);color:var(--text);}

button{cursor:pointer;font-weight:600;transition:.3s}

button:hover{transform:translateY(-2px)}

.add{background:linear-gradient(135deg,var(--accent),#0ea5e9);border:none}

.toggle{width:auto;padding:6px 12px}

#error{color:#ef4444;display:none;font-size:.9rem;margin-top:5px}

.error{border-color:#ef4444 !important;animation:shake .3s}

@keyframes shake{0%{transform:translateX(0)}25%{transform:translateX(-4px)}50%{transform:translateX(4px)}75%{transform:translateX(-4px)}100%{transform:translateX(0)}}

.sidebar{position:sticky;top:30px;}

.folder{padding:8px;margin-bottom:6px;border-radius:8px;cursor:pointer;background:var(--input);transition:.3s}

.folder:hover,.folder.active{background:var(--accent);color:#000}

ul{list-style:none;padding:0}

li{display:flex;gap:10px;align-items:center;padding:10px;margin-bottom:10px;background:var(--input);border-radius:10px;animation:fade .3s ease;}

li:hover{transform:scale(1.02)}

@keyframes fade{from{opacity:0;transform:translateY(5px)}to{opacity:1}}

a{color:var(--accent);text-decoration:none;font-weight:500}

.favicon{width:20px;height:20px}

.actions{margin-left:auto;display:flex;gap:8px}

.action{cursor:pointer}

.delete{color:#ef4444}

.edit{color:#facc15}

@media(max-width:900px){.left-card{position:relative;transform:none;width:100%;left:0;margin-bottom:20px}.right-area{margin-left:0;padding:20px}.bookmark-layout{grid-template-columns:1fr}}

</style>

</head>

<body>

  

<div class="left-card card">

<h1 id="formTitle">➕ Add Bookmark</h1>

<button class="toggle" onclick="toggleTheme()">🌙 / ☀️</button>

  

<input id="name" placeholder="Website name">

<input id="url" placeholder="https://example.com">

<input id="folder" placeholder="Folder">

  

<p id="error"></p>

  

<button class="add" onclick="saveBookmark()">Save</button>

<button onclick="exportData()">📤 Export</button>

<input type="file" onchange="importData(event)">

</div>

  

<div class="right-area">

<div class="bookmark-layout">

<div class="card sidebar">

<h2>📁 Folders</h2>

<div id="folders"></div>

</div>

  

<div class="card">

<h2>🔖 My Bookmarks</h2>

<input placeholder="🔍 Search..." oninput="search(this.value)">

<ul id="list"></ul>

</div>

</div>

</div>

  

<script>

let bookmarks = JSON.parse(localStorage.getItem("bookmarks")) || [];

let theme = localStorage.getItem("theme") || "dark";

let editIndex = null;

let activeFolder = "All";

  

document.body.className = theme === "light" ? "light" : "";

  

// Get DOM elements

const nameInput = document.getElementById("name");

const urlInput = document.getElementById("url");

const folderInput = document.getElementById("folder");

const errorMsg = document.getElementById("error");

const list = document.getElementById("list");

const foldersDiv = document.getElementById("folders");

const formTitle = document.getElementById("formTitle");

  

function toggleTheme(){

    document.body.classList.toggle("light");

    localStorage.setItem("theme",

        document.body.classList.contains("light") ? "light" : "dark");

}

  

function saveBookmark(){

    errorMsg.style.display = "none";

    nameInput.classList.remove("error");

    urlInput.classList.remove("error");

  

    if(!nameInput.value.trim()){

        showError("Website name is required", nameInput); return;

    }

    if(!urlInput.value.trim()){

        showError("Website URL is required", urlInput); return;

    }

    try{new URL(urlInput.value);} catch{showError("Invalid URL format", urlInput); return;}

  

    const data = {

        name: nameInput.value.trim(),

        url: urlInput.value.trim(),

        folder: folderInput.value.trim() || "General"

    };

  

    if(editIndex !== null){

        bookmarks[editIndex] = data;

        editIndex = null;

        formTitle.textContent = "➕ Add Bookmark";

    } else {

        bookmarks.push(data);

    }

  

    nameInput.value = urlInput.value = folderInput.value = "";

    errorMsg.textContent = "";

    errorMsg.style.display = "none";

  

    save();

    render();

}

  

function showError(msg, field){

    errorMsg.textContent = msg;

    errorMsg.style.display = "block";

    field.classList.add("error");

}

  

function editBookmark(i){

    const b = bookmarks[i];

    nameInput.value = b.name;

    urlInput.value = b.url;

    folderInput.value = b.folder;

    editIndex = i;

    formTitle.textContent = "✏️ Edit Bookmark";

}

  

function deleteBookmark(i){

    bookmarks.splice(i,1);

    save();

    render();

}

  

function save(){localStorage.setItem("bookmarks", JSON.stringify(bookmarks));}

  

function render(search=""){

    list.innerHTML="";

    const filtered = bookmarks.filter(b=>

        (activeFolder==="All"||b.folder===activeFolder) &&

        b.name.toLowerCase().includes(search)

    );

    filtered.forEach((b,i)=>{

        list.innerHTML += `

        <li>

            <img class="favicon" src="https://www.google.com/s2/favicons?domain=${b.url}">

            <div>

                <a href="${b.url}" target="_blank">${b.name}</a><br>

                <small>📁 ${b.folder}</small>

            </div>

            <div class="actions">

                <span class="action edit" onclick="editBookmark(${i})">✏️</span>

                <span class="action delete" onclick="deleteBookmark(${i})">✖</span>

            </div>

        </li>`;

    });

    renderFolders();

}

  

function renderFolders(){

    const fs = ["All", ...new Set(bookmarks.map(b=>b.folder))];

    foldersDiv.innerHTML="";

    fs.forEach(f=>{

        foldersDiv.innerHTML += `<div class="folder ${f===activeFolder?'active':''}" onclick="activeFolder='${f}';render()">${f}</div>`;

    });

}

  

function search(v){render(v.toLowerCase());}

  

function exportData(){

    let a = document.createElement("a");

    a.href = URL.createObjectURL(new Blob([JSON.stringify(bookmarks,null,2)]));

    a.download = "bookmarks.json"; a.click();

}

  

function importData(e){

    let r = new FileReader();

    r.onload=()=>{bookmarks=JSON.parse(r.result); save(); render();}

    r.readAsText(e.target.files[0]);

}

  

// Clear errors while typing

[nameInput, urlInput, folderInput].forEach(i => i.addEventListener("input", ()=>{

    errorMsg.style.display="none";

    i.classList.remove("error");

}));

  

render();

</script>

</body>

</html>
```
### Step 3: Create the Backend (server.js)
Create `server.js`:

```js
const express = require('express');
const sqlite3 = require('sqlite3');
const path = require('path');
const app = express();

// Middleware
app.use(express.json());
app.use(express.static('public')); // Serve the HTML + assets

// Open (or create) SQLite database
const db = new sqlite3.Database('bookmarks.db');

// Create table if not exists
db.run(`
  CREATE TABLE IF NOT EXISTS bookmarks (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    url TEXT NOT NULL,
    folder TEXT NOT NULL
  )
`);

// API Routes
app.get('/api/bookmarks', (req, res) => {
  db.all('SELECT id, name, url, folder FROM bookmarks ORDER BY id DESC', (err, rows) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(rows);
  });
});

app.post('/api/bookmarks', (req, res) => {
  const { name, url, folder = 'General' } = req.body;
  if (!name || !url) return res.status(400).json({ error: 'name and url required' });

  db.run(
    'INSERT INTO bookmarks (name, url, folder) VALUES (?, ?, ?)',
    [name, url, folder],
    function (err) {
      if (err) return res.status(500).json({ error: err.message });
      res.json({ id: this.lastID, name, url, folder });
    }
  );
});

app.put('/api/bookmarks/:id', (req, res) => {
  const { name, url, folder } = req.body;
  const { id } = req.params;

  db.run(
    'UPDATE bookmarks SET name = ?, url = ?, folder = ? WHERE id = ?',
    [name, url, folder, id],
    function (err) {
      if (err) return res.status(500).json({ error: err.message });
      if (this.changes === 0) return res.status(404).json({ error: 'Not found' });
      res.json({ id: parseInt(id), name, url, folder });
    }
  );
});

app.delete('/api/bookmarks/:id', (req, res) => {
  const { id } = req.params;
  db.run('DELETE FROM bookmarks WHERE id = ?', [id], function (err) {
    if (err) return res.status(500).json({ error: err.message });
    if (this.changes === 0) return res.status(404).json({ error: 'Not found' });
    res.json({ deleted: true });
  });
});

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### Step 4: package.json
Create `package.json`:

```json
{
  "name": "shared-bookmark-manager",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "sqlite3": "^5.1.7"
  }
}
```

Install dependencies (once):

```bash
npm install
```

### Step 5: Modify Your index.html JavaScript
Replace the entire `<script>` section in your `index.html` with this updated version that talks to the backend:

```html
<script>
let bookmarks = [];
let editId = null;
let activeFolder = "All";

const nameInput = document.getElementById("name");
const urlInput = document.getElementById("url");
const folderInput = document.getElementById("folder");
const errorMsg = document.getElementById("error");
const list = document.getElementById("list");
const foldersDiv = document.getElementById("folders");
const formTitle = document.getElementById("formTitle");

async function loadBookmarks() {
  const res = await fetch('/api/bookmarks');
  bookmarks = await res.json();
  render();
}

function saveBookmark() {
  errorMsg.style.display = "none";
  nameInput.classList.remove("error");
  urlInput.classList.remove("error");

  if (!nameInput.value.trim()) return showError("Website name is required", nameInput);
  if (!urlInput.value.trim()) return showError("Website URL is required", urlInput);
  try { new URL(urlInput.value); } catch { return showError("Invalid URL format", urlInput); }

  const data = {
    name: nameInput.value.trim(),
    url: urlInput.value.trim(),
    folder: folderInput.value.trim() || "General"
  };

  if (editId !== null) {
    // Update
    fetch(`/api/bookmarks/${editId}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    }).then(() => {
      editId = null;
      formTitle.textContent = "➕ Add Bookmark";
      clearForm();
      loadBookmarks();
    });
  } else {
    // Create
    fetch('/api/bookmarks', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    }).then(() => {
      clearForm();
      loadBookmarks();
    });
  }
}

function editBookmark(id) {
  const b = bookmarks.find(bm => bm.id === id);
  nameInput.value = b.name;
  urlInput.value = b.url;
  folderInput.value = b.folder;
  editId = id;
  formTitle.textContent = "✏️ Edit Bookmark";
}

function deleteBookmark(id) {
  if (!confirm("Delete this bookmark?")) return;
  fetch(`/api/bookmarks/${id}`, { method: 'DELETE' }).then(() => loadBookmarks());
}

function clearForm() {
  nameInput.value = urlInput.value = folderInput.value = "";
}

function showError(msg, field) {
  errorMsg.textContent = msg;
  errorMsg.style.display = "block";
  field.classList.add("error");
}

function render(search = "") {
  list.innerHTML = "";
  const filtered = bookmarks.filter(b =>
    (activeFolder === "All" || b.folder === activeFolder) &&
    b.name.toLowerCase().includes(search.toLowerCase())
  );

  filtered.forEach(b => {
    const li = document.createElement("li");
    li.innerHTML = `
      <img class="favicon" src="https://www.google.com/s2/favicons?domain=${encodeURIComponent(b.url)}">
      <div>
        <a href="${b.url}" target="_blank">${b.name}</a><br>
        <small>📁 ${b.folder}</small>
      </div>
      <div class="actions">
        <span class="action edit" onclick="editBookmark(${b.id})">✏️</span>
        <span class="action delete" onclick="deleteBookmark(${b.id})">✖</span>
      </div>
    `;
    list.appendChild(li);
  });

  renderFolders();
}

function renderFolders() {
  const fs = ["All", ...new Set(bookmarks.map(b => b.folder))];
  foldersDiv.innerHTML = "";
  fs.forEach(f => {
    const div = document.createElement("div");
    div.className = `folder ${f === activeFolder ? 'active' : ''}`;
    div.textContent = f;
    div.onclick = () => { activeFolder = f; render(); };
    foldersDiv.appendChild(div);
  });
}

function search(v) { render(v); }

// Clear errors while typing
[nameInput, urlInput, folderInput].forEach(i => i.addEventListener("input", () => {
  errorMsg.style.display = "none";
  i.classList.remove("error");
}));

// Initial load
loadBookmarks();
</script>
```

### Step 6: Run It Locally First
```bash
npm start
```
Open http://localhost:3000 → All users on the same network (or anyone with access) will see and edit the **same shared bookmarks**.

### Step 7: Dockerize the Full App (Recommended for Server)
Create a `Dockerfile` in the project root:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

Build and run:

```bash
docker build -t shared-bookmarks .
docker run -d -p 3000:3000 --name shared-bookmarks shared-bookmarks
```

Now access it at `http://your-server-ip:3000` from any device/browser → **everyone shares the same bookmarks**.

# For Ubuntu/Debian
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs

cd /_docker/shared-bookmark-manager
npm install
npm start

**Just build and run with Docker (no npm needed on host)**
docker build -t shared-bookmarks .
docker run -d -p 3000:3000 --name shared-bookmarks shared-bookmarks