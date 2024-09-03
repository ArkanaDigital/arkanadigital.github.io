# Project - Project Name
Project Name - Customer Name

## Workflow for Technical member
- Decide 'main' branch on servers:
  Contoh: 17.0, 17.0-staging, 17.0-development
- Before doing development, checkout from 'main' branch. Please pay attention to 'Naming Branch'-rule.
- Flag development status 'In Progress' on developer task list / requirement list / FSD list / etc
- Use pre-commit before commit, please discuss pre-commit ignore if needed.
- Testing feature on localhost
- Create new 'Pull Request' from branch feature/ improvement/ fix/ refactor to destinated branch. If there is more than one server, staging and production, do PR from feature to development, after tested do PR from feature to main branch.
- Flag development status 'Pull Request' on developer task list / requirement list / FSD list / etc

## Workflow for Lead Technical
- Start repository with copier and pre-commit (example: https://github.com/ArkanaDigital/addons-repo-template).
- New repository must contain readme.md (this template), requirements.txt, and .pre-commit-config.yaml file decided by team member.
- Invite all technical member to new repository
- Manage PR created by technical member
- Deploy to servers and update the development status to QC / Deploy / Done

# Naming Branch
Berikut adalah konvensi penamaan branch yang digunakan dalam proyek ini:

- `feat-nama_feature`: Digunakan untuk branch yang menambahkan fitur baru.  
  Example: `17.0-feat-tambah_login`

- `imp-feature_apa`: Digunakan untuk branch yang melakukan peningkatan pada fitur yang sudah ada.  
  Example: `17.0-imp-optimalisasi_kinerja_login`

- `fix-feature_apa`: Digunakan untuk branch yang memperbaiki bug.  
  Example: `17.0-fix-perbaikan_bug_login`

- `refactor-feature_apa`: Digunakan untuk branch yang melakukan refactoring kode tanpa mengubah fungsionalitas.  
  Example: `17.0-refactor-login_module`

## Repo Odoo that Used for Development :
- [Download] ODOO CE (https://drive.google.com/file/d/1qkucGxsTeGERVff0CRP1BePWf8FhuApU/view?usp=sharing or github repo url on specific active branch & specific commits)
- [Download] ODOO EE (https://drive.google.com/file/d/1qkucGxsTeGERVff0CRP1BePWf8FhuApU/view?usp=sharing or github repo url on specific active branch & specific commits)
- Extra repository: github repo url (bulog_extra, modinity_extra, etc.)

## Database backup:
- [Download] Database backup per 03/09/2024 (google drive url)

## VSCode Configuration:
- Example of vscode configuration on workspace files:
  "settings": {
    "python.linting.pylintEnabled": false,
    "python.linting.flake8Enabled": true,
    "python.linting.lintOnSave": true,
    "python.linting.flake8Args": ["--ignore=E501,E301,E302"],
    "python.formatting.provider": "black",
    "editor.formatOnSave": true,
    "editor.formatOnPaste": false,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "[xml]": {
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    },

## Other Required Package that Used for Development :
- **Python** : Python 3.12
- **PostgreSQL** : V15.6 or Newer
- **wkhtmltopdf** : wkhtmltopdf 0.12.5 (with patched qt)
    - **Linux** : 
    ```bash
    sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
    sudo apt install ./wkhtmltox_0.12.5-1.bionic_amd64.deb
    ```

    - **MacOS** : 
    ```bash
    https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox-0.12.5-1.macos-cocoa.pkg
    Click or Extract the Downloaded File, then Install
    ```

    - **Windows** :
    ```bash
    https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox-0.12.5-1.msvc2015-win64.exe
    Click or Extract the Downloaded File, then Install
    ```

## Required Rules for Development :
- **Module Name** : <customer_name>_feature 
    - **Example** : medika_hr, medika_hr_payroll, arkana_sale, aba_pos_loyalty, margaria_website, mid_sale, etc

- Add or Edit **requirements.txt** File if you add new Library that using on Python File 

- **Nice to Have** - Try to add Demo Data, Do Unit Test, and Documentation after Finish Develop Module 

- **Must Have** - Try to add Comments or Description for the Function that Created

- Always Create **README.md** File inside Custom Module to Explain or Summarize the Custom Module's Purpose

- **Folder Structure**
    - Controllers : Module that contains Python File / Code for Client Side's Process
    - Data : Module that contains Real / Default / Template Data
    - Demo : Module that contains Dummy Data for Testing
    - Doc : Module that contains Documentation of Module's Purpose / Workflow
    - Models : Module that contains Python File / Code for Module's Main Purpose / Workflow
    - Report : Module that contains Python & XML File for Reporting especially PDF Report
    - Routes : Module that contains Python File / Code for API Service
    - Security : Module that contains Security File for Access Right & Record Rules
    - Static : Module that contains for Asset Management, Library, JS File, XML Template, and SCSS for Style
    - Tests : Module that contains Unit Tests for the Module
    - Tools : Module that contains General Function / Class that be used for General Purpose
    - Views : Module that contains XML File to define View for Backend Odoo and Template for Website
    - Wizard : Module that contains Python & XML File for Spesific Purpose that showed by Modal View
    - __init__.py
    - __manifest__.py : File that contains information & data from the Module
    - __hooks__.py : File that contains Function that execute when Module is in Installing / Uninstalling Process

- **Manifest** :
```Python
{
    "name": "Module Name",
    "summary": "Module Summary",
    "description": "Module Description",
    "author": "PT Arkana Solusi Digital",
    "maintainers": ["author_github"],
    "website": "https://arkana.co.id",
    "category": "Customizations",
    "version": "17.0.1.0.0", #Using 17.0.1.0.1 if any updates or improves features
    "sequence": 10,
    "depends": [
        "base", 
    ],
    "license": "LGPL-3",
    "data": [
        "security/ir.model.access.csv",
    ],
    "demo": [
        "data/model_demo.xml",
    ],
    "assets": {
        "web.report_assets_common": [
            "module/static/src/scss/module_report.scss",
        ],
    },
    # "pre_init_hook": "pre_init_hook",
    # "post_init_hook": "post_init_hook",
    "application": True,
    "installable": True,
    "auto_install" : False,
    "external_dependencies": {"python": []},
}
```

**Imports**

The imports are ordered as

    1. External libraries (one per line sorted and split in python stdlib)

    2. Imports of odoo

    3. Imports from Odoo modules (rarely, and only if necessary)

Inside these 3 groups, the imported lines are alphabetically sorted.

```
# 1 : imports of python lib
from datetime import datetime
# 2 : imports of odoo
import odoo
from odoo import api, fields, models, _ # alphabetically ordered
# 3 : imports from odoo addons
from odoo.addons.web.controllers.main import login_redirect
```
Based on Odoo's [coding guidelines](https://www.odoo.com/documentation/17.0/contributing/development/coding_guidelines.html) and focusing specifically on naming conventions for models, methods, variables, and fields in Python, here’s a clear "Do and Don't" format:

### Naming Conventions for Python Code in Odoo

#### **Model Names**
**Do:**
- **Use CamelCase for Model Names**: Models should be named using CamelCase to maintain clarity and consistency.
  ```python
  class SaleOrder(models.Model):
      _name = 'sale.order'
  ```
- **Be Descriptive**: Choose names that clearly describe the purpose of the model.
  ```python
  class ProjectTask(models.Model):
      _name = 'project.task'
  ```

**Don't:**
- **Avoid Using Underscores in Class Names**: Model names should be in CamelCase, not snake_case.
  ```python
  class sale_order(models.Model):  # Wrong
      _name = 'sale.order'
  ```

#### **Method Names**
**Do:**
- **Use Snake Case for Method Names**: Methods should be named using snake_case.
  ```python
  def compute_total_amount(self):
      # Method logic
  ```
- **Be Descriptive and Specific**: Method names should clearly describe what they do.
  ```python
  def create_customer_invoice(self):
      # Method logic
  ```

**Don't:**
- **Avoid Using CamelCase for Method Names**: Consistency with snake_case improves readability and aligns with Python's conventions.
  ```python
  def computeTotalAmount(self):  # Wrong
      # Method logic
  ```

#### **Variable Names**
**Do:**
- **Use Snake Case for Variables**: Variables should be named using snake_case.
  ```python
  total_amount = 100
  employee_ids = self.env['hr.employee'].search([])
  ```
- **Be Descriptive**: Choose variable names that convey their purpose or content.
  ```python
  employee_name = record.name
  ```

**Don't:**
- **Avoid Using Single Letter Variables for Descriptive Content**: Use meaningful names instead of single letters.
  ```python
  a = self.env['sale.order'].search([])  # Wrong
  orders = self.env['sale.order'].search([])  # Correct
  ```

#### **Field Names**
**Do:**
- **Use Snake Case for Field Names**: Fields should be named using snake_case.
  ```python
  name = fields.Char(string='Name')
  date_of_birth = fields.Date(string='Date of Birth')
  ```
- **Be Descriptive**: Field names should clearly describe the data they hold.
  ```python
  customer_address = fields.Char(string='Customer Address')
  ```

**Don't:**
- **Avoid Using CamelCase for Field Names**: Consistency with snake_case ensures clarity and consistency.
  ```python
  customerAddress = fields.Char(string='Customer Address')  # Wrong
  ```

### General Guidelines

**Do:**
- **Follow Consistent Naming**: Ensure consistency in naming conventions across models, methods, and fields.
- **Use Clear and Descriptive Names**: Aim for names that clearly indicate the purpose or content.
- **Adhere to Odoo's Conventions**: Follow Odoo’s established practices for naming and code structure.

**Don't:**
- **Avoid Abbreviations**: Use full words instead of abbreviations to make names more descriptive and understandable.
  ```python
  # Instead of "desc", use "description"
  description = fields.Text(string='Description')
  ```

By adhering to these guidelines, you ensure that your code is consistent, readable, and aligned with Odoo’s standards, making it easier to maintain and collaborate on.
