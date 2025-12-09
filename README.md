# 🗄️ | inventorETB
**inventorETB** is a Shopify inventory compiler that compiles the current value of the inventory using different metrics.

# 🌐 | Web Version (Recommended)

The easiest way to use inventorETB is through the web interface - no installation required!

**👉 [Open inventorETB Web App](https://alexgirarddev.github.io/inventorETB/)**

### How to Use:
1. Export your inventory data from Shopify (see instructions below)
2. Open the web app link above
3. Upload your CSV file
4. Click "Process Inventory"
5. Download your filtered reports and summary

That's it! No Python, no setup, just drag and drop.

---

# 🐍 | Python Version (Alternative)

If you prefer to run this locally with Python:

### Make sure you have the following installed:
- [VS Code](https://code.visualstudio.com/Download)
- [Python](https://www.python.org/downloads/)

### Setup

1. Clone the repository. Make sure to replace `your-username` with your Github username or copy the directory from the Github repository page.

```bash
git clone https://github.com/your-username/inventorETB.git
cd inventorETB
```

<img width="1278" height="477" alt="image" src="https://github.com/user-attachments/assets/acef6061-6b53-49ee-85ee-925a0ecb71a4" />


2. Create and activate a virtual environment (venv) using the terminal. The terminal can be opened using the header or ``Ctrl + Shift + ` ``.
```bash
python -m venv venv
source venv/bin/activate   # macOS/Linux
venv/Scripts/activate      # Windows
```

3. Install dependencies. Check that your current path is the same as where requirements.txt and all of the other files are located.
```bash
pip install -r requirements.txt
```

### Exporting Data from Shopify

4. Copy the following ShopifyQL query into the ShopifyQL Editor. The same code can be found under the `inventory-totals` in **Reports** (this will take a while)
```sql
FROM inventory
  SHOW ending_inventory_units, ending_inventory_retail_value, product_collection
  GROUP BY product_title, product_variant_id, product_variant_title,
    product_collection WITH TOTALS, GROUP_TOTALS, CURRENCY 'CAD'
  DURING today
  ORDER BY product_variant_id ASC
  LIMIT 1000
```

5. Download the results as a CSV file.
<img width="1640" height="445" alt="image" src="https://github.com/user-attachments/assets/ce1ec009-d1ac-48eb-bf8c-aea283b9b614" />

### Running inventorETB

6. Place the downloaded CSV in the project’s folder (or wherever specified in `config.py`).

7. Configurate `config.py` to match the information that you need. 

8. Run the processing script. This could take a while depending on the size of the data:
```bash
python processQL.py
```

# 🛠️ | Project Structure
```
inventorETB/
│── index.html              # Web application (GitHub Pages)
│── processQL.py            # Main processing script (Python)
│── config.py               # Configurations for Python version
│── requirements.txt        # Python dependencies
│── .github/workflows/      # GitHub Actions for auto-deployment
│── README.md               # Documentation
```

# 🔧 | Setting Up GitHub Pages

The web version is automatically deployed via GitHub Actions. To enable it:

1. Go to your repository Settings → Pages
2. Under "Source", select "GitHub Actions"
3. Push any changes to the `main` branch - the site will auto-deploy
4. Your app will be available at: `https://your-username.github.io/inventorETB/`

The workflow file (`.github/workflows/deploy.yml`) is already configured and will run automatically on every push to main.

# 🤝 | Contributing
Pull requests are welcome! For major changes, please open an issue first to discuss what you’d like to add or change.

# 📄 | License
This project is licensed under the MIT License – see the LICENSE file for details.


