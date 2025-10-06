# 🧩 Exclusion List Automation (ETL Pipeline)

## 📘 Overview
This project automates the generation of **exclusion lists** for retail promotional or discount campaigns.  
Previously, this process was completely **manual** — analysts had to handle 5–6 Excel files shared weekly by the client, apply complex business rules, and manually merge them to generate the final exclusion list.

This ETL pipeline, built entirely in **Python (Jupyter Notebook)**, automates the entire workflow:
- Automatically **extracts** input files from client emails  
- **Transforms** and merges data using custom business logic  
- **Loads** the final exclusion list that’s ready to be shared back with the client  

This automation reduced a multi-hour manual process to a few minutes of execution.

---

## ⚙️ ETL Flow

### **1️⃣ Extract**
- Uses `imap_tools` and `email` libraries to connect securely to a Gmail inbox.
- Searches for recent emails (last 14 days) with subjects like “Input File Requirement.”
- Downloads all Excel attachments (Sales Master, Article Master, Style Master, etc.) to a local `attachments/` folder.
- Automatically creates the folder if it doesn’t exist (`os.makedirs(download_folder, exist_ok=True)`).

💡 **Shortcut used:** IMAP-based dynamic email filtering and automated attachment extraction — no manual downloads required.

---

### **2️⃣ Transform**
Once the input files are available, multiple transformation steps are applied using **pandas** and **Excel utilities** (`openpyxl`, `pyxlsb`).

**Key transformation logic:**
- 🧹 **Data Cleaning:**  
  Removes duplicates, standardizes column names, and handles missing store/article IDs.  
- ⚖️ **Rule-Based Filtering:**  
  Applies discount thresholds, product-type logic, and promotion-based style inclusion/exclusion.  
- 🔁 **Cross-Joins and Mapping:**  
  Performs cross-joins between Article and Store masters to map eligible articles to stores efficiently using `pd.merge(..., how='cross')`.  
- 🕓 **Historical Pullback Check:**  
  Compares with past pullback lists to ensure previously excluded items remain excluded.  
- 🔗 **Progressive Merging:**  
  Sequentially merges all intermediate outputs into a single **Merged_Exclusion_List** DataFrame.

💡 **Optimizations / Shortcuts:**
- Used `how='cross'` instead of nested loops for fast many-to-many joins.  
- Vectorized operations & list comprehensions for better performance.  
- Used DataFrame shape tracking (`print(df.shape)`) for debugging instead of full prints.  

---

### **3️⃣ Load**
- Writes the final output as an Excel file:
- Stores output in the `output/` folder.
- Can be extended to auto-send via email using SMTP (future enhancement).

✅ **ETL Summary:**
| Stage | Description |
|--------|--------------|
| **Extract** | Downloads latest client files via email |
| **Transform** | Cleans, filters, merges, and applies business logic |
| **Load** | Exports the final exclusion list for reporting or mail delivery |

---

## 📈 Outcome
- Automated a complex manual reporting process that took **hours** down to **minutes**.  
- Achieved near **zero manual intervention** after setup.  
- Improved accuracy and consistency across weekly runs.  
- Modular notebook structure allows easy debugging or rule changes.

---

## 🧠 New Learnings & Libraries Used
| Category | Library / Concept | Key Learnings |
|-----------|------------------|----------------|
| **Email Automation** | `imap_tools`, `email`, `os` | Fetching attachments securely from Gmail |
| **Data Processing** | `pandas`, `openpyxl`, `pyxlsb` | Efficient merging, filtering, and cross-joins |
| **Date Handling** | `datetime`, `timedelta` | Dynamic filtering of emails by date |
| **File Management** | `os.makedirs`, `pathlib` | Automatic folder creation and management |
| **Debugging** | `print`, shape tracking | Fast checkpointing after every step |
| **ETL Design** | Modular scripting | Clear separation between extract, transform, and load |

---

## 🚀 Future Enhancements
- Schedule weekly runs using **Apache Airflow** or **cron jobs**.  
- Add email automation for sending output files back to clients.  
- Integrate Power BI or Streamlit dashboard for quick visual validations.

---

## 🗂️ Repository Structure

Exclusion_List_Automation/
│
├── notebooks/
│ └── Exclusion_List_Automation.ipynb
│
├── data/
│ └── (sample input files – no confidential data)
│
├── output/
│ └── Merged_Exclusion_List_YYYYMMDD.xlsx
│
├── README.md
└── .gitignore


---

> 💬 **Summary:**  
> This project showcases an **end-to-end ETL automation** solving a real-world business bottleneck — transforming a repetitive manual task into a fast, reliable, and scalable solution using Python.


