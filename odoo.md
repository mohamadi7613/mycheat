
# Odoo

```bash
sudo apt update
sudo apt install build-essential wget git python3-pip python3-dev python3-wheel libfreetype6-dev libxml2-dev libzip-dev libssl2-dev python3-setuptools libjpeg-dev zlibig-dev libpq-dev libxslt1-dev libldap2-dev libtiff5-dev libopenjp2-7-dev
sudo apt-get install git python3 python3-pip build-essential wget python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libssl2-dev python3-setuptools node-less libjpeg-dev gdebi -y
sudo apt-get install libpq-dev libxml2-dev libxslt1-dev libldap2-dev libssl2-dev libffi-dev
sudo pip3 install Babel decorator docutils ebaysdk feedparser gevent greenlet htmlztext linja2 lxml Mako MarkupSafe mock num2words ofxparse passlib pillow psutil psvcogreen psvcopg2 pydot pyparsing PyPDF2 pyserial python-dateutil python-openid pytz pyusb PyVAML qrcode reportlab requests six suds-jurko vatnumber vobject herkzeug XlsxWriter xlwt xlrd polib
sudo apt install postgresql
sudo su - postgres -c "createuser -s odoo"       # user
sudo apt-get install xfonts-75dpi
sudo wget https://github.com/ukhtmitox/packaging/releases/download/0.12.6-1/ukhtmitox_0.12.6-1.bionic_amd64.deb
sudo dpkg -i ukhtmitox_0.12.6-1.bionic_amd64.deb
sudo apt install ukhtmitcpdf
sudo apt-get install -y npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
sudo npm install -g less less-plugin-clean-css
sudo npm install -g rtless
sudo apt-get install -y node-less
sudo python3 -m pip install libsass
sudo apt-get install -y git
git clone https://www.github.com/odoo/odoo --depth 1 --branch=16.0 --single-branch
```

### start
1. docs: https://www.odoo.com/documentation/18.0/administration/on_premise/source.html
2. install requierments.txt
3. install postgresql and create user `odoo` (user should have login privilage and create db privilage)
4. start postgresql using: `sudo service postgresql start` in linux or `net start postgresql-x64-16` in windows
5. start odoo with `python odoo-bin` and you see `database selector page`
6. if you start odoo with `python odoo-bin -r odoo -w PASSWORD --addons-path=addons -d mydb` you will see `login page`
7. open browser and go to http://localhost:8069 and in login page use user=admin and password=admin
8. if you install odoo with package, odoo always be accessilbe in that url in the browser

## command line
```bash
python odoo-bin            # run odoo
```

# Older versions
In older versions we have '.odoorc' file in the home directory, but for newer versions we have 'odoo.conf' file.

## odoorc
```py
[options]
db_host = localhost
db_port = 5432
db_user = odoo
db_password = odoo
db_name = odoo
addons_path = custome_addons
```

## custome addons
```
|custome_addons\            # do not create custome addons folder inside odoo folder
|--- demo\
|--- data\
|--- security\
|--- static\
|--- views\
|--- controllers\
|--- models\
|--- templates\                  #
|---__init__.py
|---__manifest__.py
```

#### addons: 1.__manifest__.py
for each addons we a manifest file containing a dictionary that specifies the metadata for addons.
```py
{    # name is required but some of them are optional
    "name" : "Employee",          # name of addon
    "category" : "Tools",         # category of addon
    "data" : [
        "security/ir.model.access.csv",       # a file in security folder 
    ],
    "depends" : []                    #  Odoo modules which must be loaded before this one
    "demo" : [],                         # fake data if you checked in the browser for demonstratoin mode
}      
```
### addnos: 2. __init__.py
```py
from . import models
```

## Models
|models
|---property.py
|---__init__.py

#### Models: 1. property.py
```py
from odoo import api, fields, models
class Property(models.Model):
    _name = "property"                                  # name of table in db:  
    _description = "Property"
    name = fields.Char(string = "fullName", required = True)  # fullName is the name inside view
    description = fields.Text(string = "Desc", required = True)               # Text means multi line string 
    price = fields.Float(string = "expected price", readonly = True)
    number = fields.Integer(string = "Number")
    date = fields.Date(string = "Date")
    marrage = fields.Boolean(string = "Marriage", default = False)   # in selection we  have list of tupples
    direction = fields.Selection(string = "Direction", selection = [("north", "North"), ("south", "South")])
    # id, create_date, create_uid, write_date, write_uid          # these are automatically created by odoo
```

#### Models: 2. __init__.py

```py
from . import property

```

