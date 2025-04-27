

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
python odoo-bin                            # run odoo
python odoo-bin -u module_name           # update a module
python odoo-bin -i module_name           # install a module
```

## URLs
```bash
http://localhost:8069/web/database/manager
runbot.odoo.com
```

### odoo architecture : MVC
1. presentation tier : views in html and js
2. Logic tier: calculations, connecting to db in python
3. Data tier: postgresql

# Graphic tasks
1. Activate the developer mode in settings 
2. find all models in settings
3. find the id of a view from dashboard `Bug icon\Edit View: Form`

## odoo.conf
+ you can see a configg file inside: `odoo/debian/source/odoo.conf`
+ In older versions we have '.odoorc' file in linux home directory, but for newer versions we have 'odoo.conf' file.

```conf
[options]
db_host = localhost
db_port = 5432
db_user = odoo
db_password = odoo
db_name = odoo
addons_path = addons,C:\custome_addons
```

### Naming conventions
1. class names models = `ClassName`
2. File naming for models = `sale_order.py`
3. use "dot notation" for _name in class models = `module_name.model_name` like `sale.order`
4. "dot notation" for models in odoo converts to "_" in postgresql.
5. Field Naming = `lower_case_with_underscores` like `date_created`
6. Relationship Naming = use _id for `many2one` and _ids for `many2many` like `partner_ids` or `property_id`
7. Boolean fields = use `is_` like "is_published" or `can_` like "can_edit" or `has_` like "has_access"
8. method of class model = `def create_function`
9. xml files for views = `model_name.view_type` like `sale.order.form`
10. Xml ids = `model_name_view_type` like `sale_order.form`
11. Security = `model_name_access_right` like `sale_order_access_right`


## custome addons
```
|odoo\addons                # default odoo addons in source code
|custome_addons\            # do not create custome addons folder inside odoo folder
|--- demo\
|--- data\
|--- security\
|--- static\                      # for assests={} in __manifest__
|------ description\             # the name of folders and icon.png should be exactly the same
|--------- icon.png             # there is no need for import the icon, if you put the icon in this path it will display in the dashboard    
|--- views\
|--- controllers\
|--- models\
|--- templates\                  #
|---__init__.py
|---__manifest__.py
```

#### addons: 1.__manifest__.py
+ for each addons we a manifest file containing a dictionary that specifies the metadata for addons.
+ after changing the manifest file, we need to restart odoo and `Update Apps List` in `developer mdoe`.

```py
{    # name is required but some of them are optional
    "name" : "Employee",          # name of addon
    "category" : "Tools",         # category of addon
    "summary" : "Employee",       # summary of addon
    "description" : "Employee",   # description of addon
    "sequence" : 1,               # sequence number of addon in the App list [-100 is ok]
    "data" : [                     # you can find lots of samples in odoo source code ex: Sales 
        "security/ir.model.access.csv",       # a file in security folder 
    ],                             # you can see this information in the App list
    "demo" : [],                         # xml data for demo
    "depends" : []                    #  'Odoo modules' or Apps like Sales which should be loaded before this one
    "application": True,               # if it's true, this moudule will be shown in the Apps list
}     
```

### addnos: 2. __init__.py
```py
from . import models             # import all __init__ files
```

## Models
```
|models
|---property.py
|---__init__.py      # import all the models
```
+ you can check your models inside `setting/technical/models` in the dashboard
+ if you change any model, you need to restart odoo and `upgrade The App`

#### Models: 1. ModelName.py
```py
from odoo import api, fields, models
class Property(models.Model):
    _name = "property"                                  # name of table in db, we use this name in our python code
    _description = "Property"                           # _name and _description are required
    _rec_name = "name"                                  # like __str__ in django for show the name in the view [by default is name field]

    genderList = [("male", "Male"), ("female", "Female")]

    name = fields.Char(string = "fullName", required = True)  # fullName is the name inside view
    description = fields.Text(string = "Desc", required = True)               # Text means multi line string 
    price = fields.Float(string = "expected price", readonly = True)          # we can  not cahnge readonly in form-view
    number = fields.Integer(string = "Number")
    date = fields.Date(string = "Date")
    marrage = fields.Boolean(string = "Marriage", default = False)   # in selection we  have list of tupples
    gender = fields.Selection(string = "Gender", selection = [("male", "Male"), ("female", "Female")])   # second item will show to user
    gender = fields.Selection(string = "Gender", selection = genderList)   # we can use a variable inside the class 
    # id, create_date, create_uid, write_date, write_uid          # these are automatically created by odoo
```

#### Models: 2. __init__.py

```py
from . import property
```

### Models: 3. relationship
```py
class Property(models.Model):
    _name = "Property"
    author_id = fields.Many2one("res.users", string = "Author")  # Forigen key like book and author
    tag_ids = fields.Many2many("modelName", string = "tags")     # we need to create another model for relationship
    offer_ids = fields.One2manay("offerModel","property_id", string="offer_ids")      # first argument is _name of another model called comodel_name and the second argument is inverse filed
    phone = fields.Char(string="Phone", related="author_id.phone")      # "related field" should be many2one in the same model
    # phone is related filed and its inside form for new data, when user select an author_id we will see the phone number of him
class offer(models.Model):    # for One2many we need to create Many2on field in comodel 
    _name = "offer"          # Because a One2many is a virtual relationship
    property_id = Many2one("property", string="property_id")        # 
```

###### Models: 4.related field
```py
class Property(models.Model):   # You cannot chain related fields
    _name = "Property"   #   # "related field" should be many2one in the same model
    property_id = Many2one("property", string="property_id")   # related fields are not stored, not copied ,readonly, computed in superuser mode
    phone = fields.Char(string="Phone", related="property_id.phone")   # Add the attribute store=True to make it stored 
    # phone is related filed and its inside form for new data, when user select an property_id we will see the phone number of him
```

### Models: 4. Domain
we can use domain in : 1. models 2. xml views
```py
class Property(models.Model):             # Domains in Odoo are used to filter and restrict data like sql query
    _name ="property"                     # like, ilike (case insensitive), in (list of values), not in (list of values)
    _description = "Property"             # exp: domain = [("id", "in", [1, 2, 3])]
    buyer_id = fields.Many2one("res.users", string="Buyer", domain=[("is_buyer", "=", True)])    # find inside res.users where is_buyer = True
```
 using domain in xml
```xml
<filter name="filter_active" domain="[('active', '=', True)]"/>
```

### Models: 6. Computed field
onchange trigger within the form_view
computed filed trigger outside of form_view

```py
from odoo import fields, models, api               # call decorators from api

class rectangle(models.Model):            # we can calculate a computed field in 2 ways: 1. onchange decorator 2. deponds decorator
    _name = "rectangle"                    # squre is a computed filed bcz we want to calculate its value
    a= fields.Intger(string="length")       # squre is not editable in the dashboard and if you want to do this we need "inverse function"
    b= fields.Intger(string="width")       # with compute attribute link the field to the function
    squre= fields.Intger(string="squre", compute= "_compute_squre", inverse="_inverse_squre")   # you can also link without string 
    # if you link function to field without string you should define function before the field
    @api.deponds("a","b")          # deponds on what?
    def _compute_squre(self):              # computed fileds does not save in db
        for s in self:
            s.squre = s.a * s.b

    def _inverse_squre(self):         # determie a base on squre [bcz we want user can edit squre field in the dashbaord]
        for s in self:
            s.a= s.squre / s.b        # save to see the cha# link the field to the functionnge

    @api.onchange("a","b")             # @onchange is similar to @deponds
    def _onchange_squre(self):         # with onchange there is no need for loop
        self.squre = self.a * self.b
    # squre= fields.Intger(string="squre")   # with onchange there is no need for field attribute of "compute"
```


### Models: 4. Abstract model

```py
class AbstractOffer(models.AbstractModel):        # plcae abstract model in the above
    _name = "abstract.model.offer"                # abstract model does not create a table in db
    _description ="simple description"

    email = fields.Char(string="Email")
    phone = fields.Char(string= "phone")

class PropertyOffer(models.Model):
    _name = "offer"                                 # we have email and phone for this model through inhritance
    _inherit = ['abstract.model.offer']          # list of abstracts
```

#### Models: 5. Transient model
an example for transient model is `import translation`  from setting of dashboard in odoo

```py
class TransientOffer(models.TransientModel):        #   temporary data storage
    _name = "transient.model.offer"                # Unlike abstract models, transient models use actual database tables
    _description ="exmpale: for reports"
    _transient_max_count = 0                     # max number of transient record in db [zero means unlimited]
    _transient_max_hours = 3.0                     # float life time
    email = fields.Char(string="Email")
    phone = fields.Char(string= "phone")

    @api.autovacuum
    def _transient_vacuum(self):          # we can unlink old records when transient_max count or hour reached
     pass
```

#### Models: 6. Inheritance
```py
class ModelName(models.Model):
    _name = "model.name"
    _inherit = "parent.model"
    price = fields.Char()         # we can overridign a field by re-daclaring it
    def method(self):             # we can override the methods of model
        return super()             # if we use super() it runs the overriding method in the parent also
```

#### Models: 7.Decorators
+ you can see all of method decorators in  Odoo code source : `odoo/api.py`

###### 1. @api.auovacuum
```py
class Property(models.Model):             # model ir.autovacuum
    _name = "property"
    _description = "Property"

    @api.auovacuum                             # its a daily cron job used for garbage-collection-like tasks
    def _clean_offers(self):                       # our code: checks the condition and delete the records
       self.search([('status', '=', 'refused')]).unlink()        # unlink is a method for deleting records
```

###### 2. @api.model
```py
class Property(models.Model):
    _name = "property"
    _description = "Property"                                              # remember define method above of the field
    date = fields.Date(string = "Date" , default = fields.Date.today())    # instead of this
    date = fields.Date(string = "Date" , default = _set_default_date())

    @api.model                  # used to define model-level methods that don't require a specific recordset to operate.
    def _set_default_date(self):
        self.date = fields.Date.today()
```

##### 3. @api.constraints
```py
from odoo.exceptions import ValidationError
@api.constrains('name', 'description')           # constraint checker
def _check_description(self):                  # @api.constrains only apply to new data but `_sql_constraints` apply to all
    for record in self:
        if record.name == record.description:      # exp: if record.age < 18 :
            raise ValidationError("Fields name and description must be different"
```

###### 4. @api.model_create_multi
```py
@api.model_create_multi                   # takes a list of dictionaries and creates multiple records at once
def create(self, vals_list):               # create() is an ORM method and with decorator we can override it
    for vals in vals_list:                # vals_list can be a list of dictionaries or a single dictionary 
        vals['name'] = vals['name'].upper()      # vals_list as an argument and as a return
    return super(ClassName, self).create(vals_list)               # return super().create() for creating
```

###### 5. @api.deponds_context
+ `@api.deponds_context` is a decorator that allows you to access the context of the current request in your methods.
+ For example, if you have a method that takes a field name as an argument, you can use the context to access the value of that field in your method.
+ `@api.deponds_context` can be used with `@api.deponds` or alone
+ `@api.deponds` only checks the fields that are in the context and `@api.deponds_context` checks values of the fields.

```py
@api.deponds_context
def _deponds_context(self, field_name):
    print(self.env.context)
    print(self._context)
    return self.env.context.get(field_name)
```
###### 6.api.ondelete
method to be executed during unlink()



### 8.Models: sql constraints
```py
class Property(models.Model):   # @api.constrains only apply to new data but `_sql_constraints` apply to all
    _name = "property"           # not recommend
    _description = "Property"
    _sql_constraints = [         # tupple('name', 'condition', 'message')
        ('name_uniq', 'unique(name)', 'name must be unique'),        # popup message in dashboard
        ('check_age', 'check(age > 18)', 'age must be greater than 18'),
    ]
```

### 9. Model: Currency field
```py
class ModelName(models.Model):
    _name= 'model.name'
    price = fields.Monetary(string='price')
    currency_id = fields.Many2one("res.currency", string='Currency', default=lambda self:self.env.user.company_id.currency_id)
```
```xml
<group>
    <field name='currency_id' invisible=1 />
    <field name='price' widget='monetary' />             <!-- see icon of dollar beside each price in form -->
</group>
```


### Environment
```py
class Property(models.Model):
    _name = "property"

    def write(self, vals):
        print(self)
        print(self.env)
        print(self.env.uid)              #user id [use this for access rights]
        print(self.env.cr)              # database cursorg
        print(self.env.context)          # current context dictionary
```


### ORM Methods
+ when we overriding some default methods like `create` or `unlink` we can use ORM methods with calling like `self.create()`
+ we have lots of ORM methods but most impotant of them are:

1. create = insert new record  [@api.model_create_multi]
2. write = update record and trigger when user updates fields of form view and save the update
3. browse= browse for multiple records and find by a specific value
4. search = search for multiple records with conditions
5. search_count = count the number of records
6. mapped = return a list of specific fields

```py
class Property(models.Model):
    _name = "property"

    def unlink(self):                 # override unlink method
        return super(Property, self).unlink()      # usefull when we want to prevent deletion of records with specififc conditions
    def read(self):                   # override read method
        return super(Property, self).read()
    def search(self):                 # override search method
        return super(Property, self).search()
    def browse(self):                 # override browse method
        return super(Property, self).browse()

    def create(self , data):          # override create method
        res = self.env["modelName"].browse(1,2,3)
        res = self.env["modelName"].browse(1,2,3).count()                   # count records ????
        res = self.env["modelName"].search([("id", "=", 1)])                # domain condition as an argument
        res = self.env["modelName"].search([("id", "=", 1)],limit=1)        # limit condition as an argument which means only return 1 record
        res = self.env["modelName"].search([("id", "=", 1)],order="name desc")        # order records by name in descending order
        res = self.env["modelName"].search_count([("id", "=", 1)])        # search_count is used to count the number of records
        res = self.env["modelName"].search([("id", "=", 1)]).mapped("phone")        # mapped["phone"] means just return this field
        res = self.env["modelName"].search([("id", "=", 1)]).filtered(lambda r: r.phone == "14")        # same mapped with a filter condition
        return super(Property, self).create(data)

    def write(self , data):           # override write method
        print(data)                   # dictionary of updated fields
        return super(Property, self).write(data)

    def action_accept_offer(self):                 # if you want to update a field using buttons you have 2 options:
        self.property_id.selling_price = self.price       # these options are the same
        self.property_id.write({'selling_price' : self.price })   # an exmpale of using orm methods for updating fields

```


## security

+ `ir.model.access.csv`
+ without .ir.model.access.csv file we can not access any module when we activate them.
+ the line above are titles and line below are values so:  id = access_estate_property ,name = access_estate_property
+ permistion flags: perm_read=1, perm_write=0, perm_create=0, perm_unlink=0
+ unlink means delete
+ give all permistions to user by writin 1
+ you can copy all the csv code in the error logs if you try to active a modeule without any security file
+ all the value items in csv file have a formula in their name: check doc
+ import the path of csv file to `data=[]` in `__manifest__.py`
+ make sure your file starts at line 1
+ if your menu and submenus have no errors but still you cant see them, maybe there is a problem in your security file 
+ for each model in one module we need to add a line in the csv values
+ group is a collection of users who share the same permistions
+ we can find access rights (ids) in: Technical/Access Rights in dashboard

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_estate_property,access_estate_property,real_estate_ads.model_estate_property,base.group_user,1,0,0,0
```

#### 1. security: user types
1. Internal user  ==> full access to odoo appilicatoin ===> with username:password  [admin:admin] login to odoo
2. Portal user  ===> access to only relevent document ===> [portal:portal]
3. Public user ===> just aceess to website and if they buy something they become a portal user

#### 2. security: create a group

+ this code creates a fields in `settings/Users and Companies/Users/YOURUSER` and also in `settings/Users and Companies/groups` with name of `Name for group of your model` .
+ this group names are pure with no access. In dashboard you can add lots of access to them.
+ we can use this groups id in security csv file
+ we can use this groups id in xml file as attribute tag: `groups=''` for tags like `<menuItem />` or `<field />`

```xml
<odoo>                                                                  <!--import this file to data=[] in manifest -->
    <record id="idModelCategory" model="ir.module.category">                <!-- give this id to ref -->
        <field name="name" > Name for group of your model </field>           <!-- you can find similar samples in odoo addons code -->
        <field name="description" > this is a description </field>
        <field name="sequence"> 50 </field>
    </record>
    <record id='group_modelname_user' model="res.groups">             <!-- role one: we can use this id in csv file -->
        <field name="name">User: Manage own property </field>
        <field name="category_id" ref="idModelCategory" />
        <field name="implied_ids" eval="[(4, ref('base.group_user'))]" />          <!-- implied_id is for group inheritance -->
    </record>                                                          <!-- 4='link', 0='create', 1='update', 2='delete', 3='unlink' -->
    <record id='group_modelname_manager' model="res.groups">        <!-- role two:  -->
        <field name="name">Manager: Manage all property </field>
        <field name="category_id" ref="idModelCategory" />
        <field name="implied_ids" eval="[(4, ref('group_modelname_user'))]" />
    </record>
</odoo>
```
#### 3. Security: creating a Technical item of security
this code creates an item in `Technical section` of `settings/Users and Companies/Users/YOURUSER` with name of `Test`.

```xml
<odoo>                                          
    <record id="id_for_item" model="res.groups"> 
        <field name="name" > Test </field>
        <field name="category_id" ref="base.module_category_hidden" />    <!-- ref is very imp -->
    </record>
</odoo>
```

#### 4.Security: Override Access Rights
+ Access Rights are the same we right in csv file
+ we can find access rights in: Technical/Access Rights in dashboard

```xml
<odoo>
    <data>                    
        <record id='moduleName.access_id_in_csv_file' model="ir.model.access">      <!-- changing the group of Access Right in the csv file -->
            <field name="group_id" ref="moduleName.GROUPNAME" />                    <!-- we can create a group and use its name  -->
        </record>
        <record id="moduleName.access_id_in_csv_file" model="ir.model.access">   <!-- chaneg Access Right for model_id -->
           <field name="name" > new Access Right </field> 
           <field name="model_id" ref=''> </field>   <!-- model_id in csv file -->
           <field name="group_id" ref=''> </field>    <!-- create a new group -->
           <field name="perm_read" >1</field>      <!-- same permissions in csv file -->
           <field name="perm_create" >0</field>    <!-- order is imp -->
           <field name="perm_write" >0</field>    <!-- we can set eval="True" instead of 1 for this tag -->
           <field name="perm_unlink" >0</field> 
        </record>
    </data>
</odoo>
```

#### 5. Security: Record Rules
show speacifc record of table

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <data>
        <record id="property_rule_for_users" model="ir.rule">
            <field name="name">Property Rule for Users</field>
            <field name="model_id" ref="model_estate_property"/>
            <field name="domain_force">[('salesperson_id', '=', user.id)]</field>
            <field name="groups" eval="[(4, ref('real_estate_ads.group_property_user'))]"/>
            <field name="perm_read">1</field>
            <field name="perm_write">1</field>
            <field name="perm_create">0</field>
            <field name="perm_unlink">1</field>
        </record>
    </data>
</odoo>
```


## Views
+ import all the views in `__manifest__.py`
+ if you change any xml file, you need to restart odoo and `upgrade The App`
+ login as admin or become `superuser` in `setting/developer mode` if you can not see your changes
+ you can check menus in the `setting/technical/menus`
+ menus should have an action to display in the view [you can add this action manualy or in odoo xml]
+ you can find lots of xml samples in own odoo addons source code
+ for every model we need a view, and a security row

#### 0. Odoo XML Tags
```
<odoo>                       # root element
<data>                        # optional
<data noupdate="1">            # only loaded when installing the module ( otherwise reloaded at install and update)
<record>	                 # Creates/updates database records
<template>	                 # Defines QWeb templates
<xpath>	                     # (XML Path Language) is an expression language that enables you to navigate through elements
<t>                           # transforms static XML into dynamic templates
```

#### 1. first step
```xml
<?xml version="1.0" encoding="utf-8" ?>
<odoo>                               <!-- root element in every xml file -->
    <menuitem                        <!-- menuitem is a odoo tag -->
    name="My_Menu"                      <!--name for display in menu bar in the dashboard -->
    id="menu_root_property_ads"          <!-- every menuitem should have an id -->
    sequence ="0"                       <!-- sequence number of menu item for ordering in the dashboard -->
    web_icon = "modulename, path/myicon.svg"         <!-- this feature is for enterprise -->
    />
    <menuitem 
    name="Clid"
    id="child_property_ads"
    parent="menu_root_property_ads"          <!-- id of parent menu item -->
    sequence ="0"
    />
</odoo>
```


##### 1. menue and submenu
+ since the `action ="myaction_id"` is not in this xml file, so we should tell the name of the module (folder name) in the `action attribute`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <menuitem name="myname" id="myid" webicon="path/icon.png">
        <menuitem name="mychild" id="mychildid" />
            <menuitem name="mychild2" id="mychild2id" action="ModuleName.myaction_id"/>     <--! action is id of record tag -->
        </menuitem>
    </menuitem>
</odoo>
```

##### 2. action file
+ since an xml file uses this record tag, we should first import this record file in `data=[]` in `__manifest__`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>             <!-- best practice for id of record = moduleName_model_TypeOfAction like: school_student_act_window" -->
        <record id="recordId" model="ir.actions.act_window">    <!--record tag is for action -->
            <field name="name">My Action</field>                 <!-- My Action is desired [field does not need id] -->
            <field name="res_model">ModelName</field>                <!-- ModelName is '_name' attribute of model -->
            <field name="view_mode">tree,form</field>          <!--view_mode and res_model are attributes for record in tree view-->
            <field name="view_id" ref="id_of_tree_view"/>   <!-- order of "tree, form" is important --> 
            <field name="help" type="html">                  <!-- the first one "tree, form" is default view for landing page -->
                <p class="o_view_nocontent_smiling_face">
                    help                                <!-- when there is no record show this -->
                </p>
            </field>
        </record>
</odoo>
```

##### 3. form_view.xml
+ when you want to add a new data from dashboard you see a `form view`
+ you can click `Edit View Form` from developer mode to see some details

```xml
<record id="recordId" model="ir.ui.view">
    <field name="name">My View</field>
    <field name="model">ModelName</field>          <!-- ModelName is '_name' attribute of model -->
    <field name="arch" type="xml">
        <form string="my form">
            <sheet>                                   <!-- white div in the background -->
                <group>                               <!-- group is like grid, this is group root-->
                    <group>                           <!-- grid for left side -->
                        <field name="first_name" />         <!-- first_name is inside our model -->
                        <field name="gender" nolabel="1"/>            <!-- gender is inside our model -->
                    </group>
                    <group>                           <!-- grid for right side -->
                        <filed name="tags" widget="many2many_tags" />          <!--convert tree view list selection to widget tags -->
                    </group>
                </group>
                <notebook>                                      <!-- we can have multiple page inside notebook -->
                    <page>                                               <!-- list group with tabs and navbar like bootstrap -->
                        <field name="something" />
                        <field name="offer_ids">                   <!-- we can access inner fields using relationships in model -->
                            <tree>                              <!-- tree is neccessary to access inner fields -->
                                <field name="price" />           <!-- offer_ids is one2many field -->
                                <field name="status" />           <!--we can add editable=True to tree tag [instead of popup] -->
                            </tree>              
                        </field>
                    </page>
                </notebook>
            </sheet>
        </form>
    </field>
</record>
```

#### 4. tree_view.xml
+ tree view means table for data

```xml
<record id="id_of_tree_view" model="ir.ui.view">      <!-- give id of tree view to action xml as a ref -->
    <field name="name">My View</field>             <!-- very similar to form_view.xml -->
    <field name="model">ModelName</field>          <!-- ModelName is '_name' attribute of model -->
    <field name="arch" type="xml">
        <tree>
            <field name="first_name" />         <!-- first_name is inside our model -->
            <field name="gender" />            <!-- gender is inside our model -->
        </tree>
    </field>
</record>
```

#### 5. buttons 

###### 5. buttons: 1. setting a value in form view

```py
class Property(models.Model):
    _name = "property"
    state = fields.Selection([("refused","Refused"),("accepted","Accepted")], string="sataus")

    def action_accept(self):                      # use this name for "name" attribute in <button/> tag
        self.state = "accepted"                    # change the value of field

    def action_refuse(self):
        self.state = "refused"

```

```xml
<form>                                            <!-- when use type="object" ensure name="" exist in your methods of model -->
    <header>                                     <!-- create a header under "menu header" in the top of dashboard -->
        <button name='action_accept' type='button' string='Accept' class='btn btn-primary'>       <!-- button type -->
        <button name='action_refuse' type='button' string='Refuse' class='btn btn-danger'>                  
        <field name='state' widget='statusbar' />                 <!-- statusbar is a menu list at right top like arrows ">>>>" -->
        <field name='state' widget='statusbar' statusbar_visible="accepted" />      <!-- widget statusbar used for selection field -->
    </header>                    <!-- statusbar_visible means just show these items if selection in the list to user -->
</form>       <!-- use class="btn" like bootstrap for colors-->

```


###### 5. buttons: 2. link to a tree view
we can create a link to a view in 2 ways: 1. xml record tag 2. python method of our model Class

> 1. using XML:

```xml
<div class="oe_button_box" name="button_box">                                        <!-- link a button to a view with action type --> 
    <button class="oe_stat_button" name='%(ModuleName.myrecordId)d' type='action' >       <!-- name format ="%()d"  -->
        <field name='first_name' string='First Name' widget='statinfo' />
    </button>
</div>
<record id="myrecordId" model="ir.actions.act_window">         <!-- pass the id of <record tag> to name of <button tag> -->
    <field name = "name"> Property </field>
    <field name = "res_model"> ModelName </field>
    <field name = "view_mode"> tree,form </field>
    <field name = "domain"> [('property_id','=', id)]</field>    <!-- in act_window we can have domain for limitaion of our data -->
</record>  <!-- domain means only show records of data where `property_id == id`  -->
```
> 2. using Python method

```xml
<div class="oe_button_box" name="button_box">                                        <!-- link a button to a view with action type --> 
    <button class="oe_stat_button" name='myreocrdId' type='button' >       <!-- type=button for python method  -->
        <field name='first_name' string='First Name' widget='statinfo' />
    </button>
</div>
```

```py
class Porperty(models.Model):     # <button type='button' name='myrecordId'  />
    _name = "property"           # 
    
    def myrecordId(self):             # like id of record tag we should pass this name to button name
        return {                       # return an object
            'type': 'ir.actions.act_window'         # like record tag
            'name' : 'Offers '                      # a desired name
            'res_model' : 'ModelName'
            'view_mode': 'tree'
            'domain': [('property_id',"=", self.id)]      # propert_id is inside ModelName
        }
```

#### 6. Sprinkles

###### 6.1 Sprinkles: color tags
```xml
<group>
    <field name='name' />
    <field name='tags_id' widget='manay2many_tags' options="{'color_field': 'color'}" />
</group>
```

###### 6.1 Sprinkles: icons
```xml
<button name='def method' type='button' icon='fa-check' class='btn btn-primary' string="accept" />     
```

###### 6.3 Sprinkles: decoration (red and green lines)
```xml
<tree editable='bottom' decoration-success="status_field == 'accepted'" decoration-danger="status_field =='refused' " >
</tree>
```

###### 6.4 Sprinkles: attrs
```xml
<group>                               <!-- add attribute with condition -->
    <field name='is_admin' />          <!-- boolean field -->
    <field name='tasks' attrs="{'invisible': [('is_admin','=','False')]}"    <!-- make invisible if is_admin is false -->
</group>
```

#### 7. Search View
```xml
<record id="recordId" model="ir.ui.view">        <!-- customise the logic of search but not the view --> 
    <field name = "name"> a desired film </field>
    <field name = "res_model"> ModelName </field>
    <field name = "view_mode"> tree,form </field>
    <field name="arch" type="xml" >
        <search>                                    <!-- search_view -->
            <field name="first_name"  />          <!-- fields for full-text serach -->
            <field name="father_name"  />          <!-- fields for full-text serach -->
            <filter string='myfilter' name='age_field' domain="[('age','=',18)]" />    <!-- for very common filter we can define a constant -->
            <separator />             <!-- this tag creates a line like <br/> in html, using when you have lots of filters -->
        </search>               <!-- filter is very common for Selection fileds -->
    </field>    <!-- by this way, when you refresh the browser your filters wont delete -->
</record>  
```

you can also creates a custome `Group by`

```xml
<search>
    <group expand="0" string="Group by">
        <filter string="status" name="group_by_status" context="{'group_by':'state_field'}"  />
    </group>
    <searchpanel>               <!--give name of filter tag to group_by attribute -->
        <field name="state_field" icon="fa-filter" group_by="group_by_status" limit="0" />     
    </searchpanel>
</search>
```

#### 7. Kanban View
```xml
<record id="window_action_Id" model="ir.actions.avt_window">        <!-- add kanban to record tag for window --> 
   <field name="view_mode">tree,kanban,form</field>         <!-- write kanban as a second element -->
</record>
<record id="recordId" model="ir.ui.view">        <!-- customise the logic of search but not the view --> 
    <field name = "name"> a desired film </field>
    <field name = "res_model"> ModelName </field>
    <field name = "view_mode"> tree,form </field>
    <field name="arch" type="xml" >       
        <kanban>                                    <!-- kanban_view -->
            <field name="first_name"  />              <!--these fileds will show inside each card -->
            <field name="age"  />
            <templates>
                <t t-name="kanban-box" >
                    <div class="oe_kanban_detail">
                        <strong class="o_kanban_record_title">                                    <!-- bold the text -->
                            <field name="last_name" />        <!--cards per last_name -->
                        </strong>
                        <div class="o_kanban_tags_section">         <!--tags in kanban -->
                            <field name="tag_ids" widget="many2many_tags" option="{'color_field':'color'}"
                        </div>
                    </div>
                </t>
            </templates>
        </kanban>        
    </field>   
</record>  
```

> quick create in kanban 
```xml
<kanban on_create="quick_create" quick_create_view="quickId" >          <!-- pass id of record to kanban tag -->
</kanban>
<record id="quickId" model="ir.ui.view">        <!-- create a small form view --> 
    <field name = "name"> a desired film </field>
    <field name = "priority"> 10 </field>         <!-- when we have two form (1. quick form 2.form-view) we need to add priority (not 1) -->
    <field name = "res_model"> ModelName </field>
    <field name="arch" type="xml" >       <!-- -->
    <form>
        <field name="first_name" />         <!-- mention required fields in quick form -->
    </form>
</record>
```

> click for details on each cards

```xml
<div class="oe_kanban_global_click">    <!-- use this as a root tag after <t> tag -->
</div>
```

> group_by kanban
```py
# <!-- we can add default_group_by="field_name" to <kanban/> tag [you can drag to change value] -->
class ModelName(models.Model):
    _name = "modelname"    # usually we use group_by="" for selection field
    field_name = fields.Selection([('value1', 'value1'), ('value2', 'value2')], default="value1", group_expand="_expand_func")
    def _expand_func(self, records, domain, order):
        return [   # for bugfix: when you delete all data in module, the button of new for creating new data will be disabled in kanban
            key for key, dummy in type(self).state.selection
        ]
```

#### 8. pivot view
```xml
<record id="quickId" model="ir.ui.view">        <!-- add "pivot" to root act_window action tag --> 
    <field name = "name"> a desired name </field>    <!-- when you add a view_mode to act_window it will create an icon in top of dashboard -->
    <field name = "res_model"> ModelName </field>
    <field name="arch" type="xml" >       <!-- -->
    <pivot>
        <field name="first_name" type="row"/>        <!-- -->
        <field name="price" type="col"/>     
        <field name="age" type="measure"/>     
        <field name="status" type="measure"/>     
    </pivot>
</record>
```
#### 9. graph view
```xml
<graph>
        <field name="first_name" type="col"/>     
        <field name="price" type="measure"/>     
</graph>
```

#### 10. calender view
```xml
<calender date_start="created_field" string="a name" mode="month">          <!-- mode can be "month" , "weeks", "days" -->
    <group>                 
        <field name="first_name" />
        <field name="price" />
    </group>              <!-- event_open_popup="true" quick_add="false" to calender tag for better ui of creating new event -->
</calender>
```

#### 11. widgets
```xml
<!------------- char -------------->
<field name="email" widget="email"/>
<field name="website" widget="url"/>
char_emojis
<!------------- number -------------->
integer: Standard integer input

float: Decimal number input

monetary: Displays with currency symbol

float_time: Displays float as time (e.g., 1.5 as 1:30)
<field name="amount" widget="monetary" options="{'currency_field': 'currency_id'}"/>
<field name="website" widget="float_time"/>
<!------------- selection -------------->
selection: Dropdown select
selection_badge
radio: Radio buttons

priority: Star rating selector
<!------------- boolean -------------->
boolean: Checkbox
boolean_toggle
boolean_button: Toggle button

boolean_favorite: Star icon toggle
<!------------- many2one -------------->
many2one: Standard dropdown

many2one_avatar: Shows image/avatar

many2one_tags: Displays as tags

selection_badge: Displays as badge buttons
<!------------- many2many -------------->
many2many: Standard multi-select

many2many_tags: Displays as tags

many2many_binary: For binary files

many2many_tags_avatar: Tags with images

kanban.many2many: Kanban-style display
<!------------- date -------------->
date: Date picker

datetime: Date and time picker

daterange: Date range selection
<!------------- binary -------------->
binary: File upload

image: Image upload with preview

pdf_viewer: PDF preview
<!------------- status -------------->
statusbar: Shows workflow states

progressbar: Progress bar display

toggle_button: On/off toggle
<!------------- formatting -------------->
html: Rich text editor

markdown: Markdown editor

code: Code editor
<!------------- visualisation -------------->
gauge: Gauge/radial display

dashboard_graph: Mini graph/chart

ace: Advanced code editor
<!------------- input -------------->
color: Color picker

domain: Domain editor

reference: Model reference selector
<!------------- List view -------------->
handle: Drag handle for sorting

badge: Colored badge display

progressbar: Inline progress bar

button: Clickable button in list
<!------------- kanban view -------------->
kanban.avatar: Circle avatar image

kanban.image: Cover image

kanban.cover: Image with overlay

kanban.ribbon: Status ribbon
```

#### 12. Views: Inheritance

##### views_inheritance: positioning with xpath
```xml
<odoo>
    <data>
        <record id="id_of_form_extended" model="ir.ui.view">
            <field name="name">name of model</field>
            <field name="model">model.name</field>
            <field name="inherit_id" ref="base.view_users_form"/>          <!-- get id of another view as ref for inheritance-->
            <field name="arch" type="xml">    <!-- position values: 'after,before,replace,inside'  -->
                <xpath expr="your_expersion" position="after">   <!-- position means add inner elements where? -->
                    <page string="name_of_tab">                  <!-- expr means which element ? -->
                        <field name="property_ids">                   <!-- property_ids is a forien-key -->
                            <tree create="0" edit="0" delete="0">
                                <field name="name"/>              <!-- access fields of forien-key -->
                            </tree>
                        </field>
                    </page>
                </xpath>
            </field>
        </record>
    </data>
</odoo>
```

##### views_inheritance: positioning with <filed/>
+ not works in all situations, but xpath works in all situations (not recommend)

```xml
<field name='name' position='after'>          <!-- a field element with a name attribute, matches the first field with the same name -->
    <field name='first_name' />             <!--  first_name is inside forein-key  -->
</field>         <!-- if we find name='FIELD_NAME' in inspect element and our forein-key will be eqaul to that(in inspect) -->
```



### Data Files
```
|---modulename\
|------ data\              # data files
|------ demo\             # demo files
```
demo files are usefull at the point of installation but data files are usefull after installation
we create data files in 2 ways: 1. xml file 2. csv file
we carete demo files only with xml files 

##### Data files : 1. xml file
```xml
<odoo>
    <data noupdate="1">         <!-- noupdate="1" to data tag just for data files but not demo files --> 
        <record id="id" model="ModelNameForFakeData">
            <field name="frist_name"> ali <field>           <!-- fields fake value -->
            <field name="last_name"> Miri <field>
        </record>
        <record id="id2" model="ModelNameForFakeData">
            <field name="frist_name"> ahmad <field>
            <field name="last_name"> Miri <field>
        </record>
    </data>
</odoo>
```

##### Data files : 1. csv file
+ name of csv file should be ="mode.name.csv"   ---> keep dot notation
+ this data will be write into postgresql Database

```csv
"id","first_name","last_name"
id1,ali,Miri
id2,ahmad,Miri
```



### IR
The "ir" prefix in Odoo models stands for "Information Repository" or "Infrastructure Records". These are core system models that handle Odoo's internal infrastructure and metadata

1. Action-Related Models
ir.actions.act_window: Window actions (views)

ir.actions.act_url: URL actions

ir.actions.act_window_close: Close window actions

ir.actions.actions: Base actions model

ir.actions.client: Client actions

ir.actions.report: Report actions

ir.actions.server: Server actions

2. View-Related Models
ir.ui.view: Views definition

ir.ui.menu: Application menus

ir.ui.view.custom: Customized views

ir.ui.scenario: Onboarding scenarios

3. Model Metadata
ir.model: Model definitions

ir.model.data: XML ID references

ir.model.fields: Field definitions

ir.model.constraint: Model constraints

ir.model.relation: Model relations (M2M tables)

4. Security Models
ir.model.access: Access control lists

ir.rule: Record rules

ir.default: Default values

ir.config_parameter: System parameters

5. Translation & Localization
ir.translation: Translations storage

ir.sequence: Number sequences

ir.cron: Scheduled actions

ir.attachment: Attachments/files

6. UI Components
ir.actions.act_window.view: Window action views

ir.ui.icon: Icons library

ir.filters: User filters

ir.qweb: QWeb templates

7. System Configuration
ir.module.module: Installed modules

ir.module.category: Module categories

ir.logging: System logs

ir.mail_server: Mail servers

8. Advanced Technical
ir.exports: Data export definitions

ir.exports.line: Export field mappings

ir.actions.todo: Wizard todo actions

ir.property: Model properties

### Actions
we can see all types of actions in setting from dashboard

1. Window Actions (ir.actions.act_window)
2. URL Actions (ir.actions.act_url)
3. Server Actions (ir.actions.server)
4. Report Actions (ir.actions.report)
5. Client Actions (ir.actions.client)
6. Scheduled Actions (ir.cron)

#### 1. Client Actions (ir.actions.client)

############ notification: creating an action using exsiting tags
```xml
<button class="oe_stat_button" name="myaction" type="object" icon="fa-handskake-o" >          <!-- use custome action -->
    <field name="offer_count" strign="something" widget="statinfo" />
</button>
```
```py
class Porpery(models.Model):
    _name ="property"

    def myaction(self):              # create an action with method of model
        return {                        # return a dictionary
            'type': 'ir.action.client',
            # 'tag': 'reload',                # this tag reloads chrome page
            # 'tag': 'apps',             # this tag redirects to apps link
            'tag': 'display_notification',  # this tag is for sending notification
            'params': {
                'titile' : _('a text for notification')
                'type': 'success',          # warning, danger
                'sticky': False
            }
        } 
```
############ cretaing a custome widget using js
```xml
<record id="myaction_id" model="ir.actions.client">            
    <!-- we can set this id for action attribute in other tags like <menuitem/> -->
    <field name = "name"> custome client action </field> 
    <field name = "tag"> custome_client_tag </field>      <!-- use this name in js -->
</record>
```
> then we need to register this tag
| static\
|---src\
|------js\
|--------- my_custome_tag.js
|------xml\
|--------- my_custome_tag.xml

```js
odoo.define('moduleName.CustomeAction', function(require){            // define a widget
    "use strict";
    var AbstractAction= require("web.AbstractAction");
    var core = require("web.core");
    var CustomeAction = AbstractAction.extend({
        template: "CustomeTemplate",
        start: function(){
            console.log("Action")
        }
    })
    core.action.registry.add("custome_client_tag", CustomeAction)
})
```
> create a template xml file

```xml
<template>      
    <t t-name="CustomeTemplate">      <!-- use this t-name in  js file -->
        <div>
            <h3> my template </h3>
        </div>
    </t>
</template>
```
> add in __manifest__ file

```py
'assets':{
    'web.assets_backend': [
        'path/my_custome_tag.js',
        'path/my_custome_template.xml'
    ]
}
```


#### 2. Server Actions (ir.actions.server)
When you select items of a table you can see server actions like 'Delete' and 'Duplicate' or 'Export'.

```xml
<record id="myaction_id" model="ir.actions.server">            
    <!-- we can set this id for action attribute in other tags like <menuitem/> -->
    <field name = "name"> custome client action </field> 
    <field name = "model_id" ref="moduleName.model_my_model_with_no_dotnotatin" />
    <field name = "binding_model_id" ref="moduleName.model_my_model_with_no_dotnotatin" />
    <field name = "binding_view_types"> list, form </field>         <!-- bind the action in form_view and list --> 
    <field name = "state"> code </field>      
    <field name = "code">
        action = model.my_method()       <!-- "model" is a keyword -->
    </field>      
</record>
```

```py
class Property(models.Model):     # what do you want if user click on your action?  like delete
    _name = 'property'           # for exmpale by this action we want to change the value of a column for all records

    def my_method(self):
        active_ids = self._context.get('active_ids', [])         # id of record of table when you click on a record
        if active_ids:
            offer_ids = self.env['modelName'].browse(active_ids)
            for offer in offer_ids:
                offer.first_name = "const"
```


#### 3. Scheduled Actions (ir.cron)
```xml
<record id="ir_cron_extend_offer_deadline" model="ir.cron">
    <field name="name">Offers: Extend Deadline Everyday</field>
    <field name="model_id" ref="real_estate_ads.model_estate_property_offer"/>
    <field name="state">code</field>
    <field name="code">model._extend_offer_deadline()</field>     <!-- method of model in py -->
    <field name="interval_number">1</field>                      <!-- freequnecy -->
    <field name="interval_type">days</field>
    <field name="numbercall">-1</field>
    <field name="active" eval='False'/>    <!-- flase means do it manualy, not automatically-->
    <field name="doall" eval='True' />     <!-- doall means if server was off or restarted do this cronjob again -->
</record>
```

```py
class Property(models.Model):        # we can run this cronjob manually in dashboard from Scheduled Actions
    _name = 'property'
    def _extend_offer_deadline(self):
        offer_ids = self.env['estate.property.offer'].search([])
        for offer in offer_ids:
            offer.validity = offer.validity + 1             # change a filed of model daily
```

#### 4. URL Actions (ir.actions.act_url)

```xml
<button class="oe_stat_button" name="action_url_method" type="object" icon="fa-handskake-o" >          <!-- open a link with button -->
    <field name="offer_count" strign="something" widget="statinfo" />
</button>
```

```py
class Property(models.Model):           
    _name = "property"
    def action_url_method(self):                  # name of button
        return {
            'type' : 'ir.actions.act_url',
            'url' : 'https://odoo.com',
            'target' : 'new',                     # open in new tab
            'target' : 'self',                     # open in the same tab
        }
```

#### 4. Report Actions (ir.actions.report)
|module\
| reports\
|--- property_report.xml
|--- reposrt_template.xml
```xml
<odoo>          <!-- import in data=[] in __manifest__ -->
    <data>       <!-- add a print icon to dashboard -->
        <record id="property_report_action" model="ir.actions.report">      <!-- we can use <report/> tag -->
            <field name="name">Property Report Action</field>
            <field name="model">modelName</field>
            <field name="report_type">qweb-pdf</field>         <!-- or qweb-html -->
            <field name="report_name">moduleName.my_template_id</field>    <!-- external id -->
            <field name="report_file">moduleName.my_template_id</field>
            <field name="print_report_name">(object._get_report_base_filename())</field>      <!-- instead of hard code, this get name of current report>
            <field name="attachment">((object.name)+'.pdf')</field>                  <!-- instead of hard code,take the name of propery -->
            <field name="binding_model_id" ref="moduleName.model_id_csv"/>
            <field name="binding_type">report</field>       <!-- means action will appear in the Print contextual menu -->
            <field name="groups_id" eval="[(4, ref('moduleName.id_of_group'))]"/> <!-- only this group access -->
        </record>
    </data>
</odoo>
```

```py
class ModelName(models.Model):             # get the name of model
    _name = 'model.name'
    def _get_report_base_filename(self):    # use this method for xml
        return 'My report name: %s' % self.name
```
> report_template.xml

```xml
<odoo>
    <data>
        <template id="my_template_id">                    <!-- use this template in report action -->
            <t t-call="web.html_container">                <!--t-call is for reference to anotehr templaet -->
                <t t-foreach='docs' t-as="o">                <!-- specifiy "o" for accessing current object -->
                    <t t-call='moduleName.anotherTemplate' />
                </t>
            </t>
        </template>
        <template id="anotherTemplate"> 
            <t t-call="web.external_layout"> 
                <div class="document">         <!-- this class comes from bootstrap -->
                    <h2 t-field="o.first_name"/>     <!-- we said we want to access Current item with "o" -->
                </div>
            </t>
        </template>
    </data>
</odoo>
```

### Translation
|moduleName\
|---i18n\
|------fa.po

1. first export out what is available in our code --> `setting/translation/export`
2. set `empty translation template`
3. put that file in `i18n/fa.po`
4. from `profile/preferences/` change the language to `fa`


### Mixins
Mixins are abstract models that have useful and often-used features that can mix with other models.


##### 1. mixin: mail
```py
class ModelName(models.Model):           # this mixins come from a model: mail.message
    _name ="model.name"
    _inherit = ['mail.thread']      # add "mail" in "deponds":[] in __manifest__
    _inherit = ['mail.activity.mixin']         # we can set planned activities like phone call, meeting in this
    price = fields.Float(string="price", tracking=True)     # tracking means if user change price we can see in chatter
```

```xml
<div class='oe_chatter'>         <!-- add this after <sheet/> tag in form-view -->
    <field name="message_follower_ids" widget="followers" />         <!-- for every new record we can chat -->
    <field name="message_ids" widget="mail_thread" />
    <field name='activity_ids' widget="mail_activity" />           <!-- this is for activity.mixin -->
</div>
```
##### 2. mixin: utm
```py
class ModelName(models.Model):   # whenever visit our website we can track him through coockies
    _name ="model.name"
    _inherit = ['utm.mixin']      # add "utm" in "deponds":[] in __manifest__
```
```xml
<group>           <!-- put this in form-view -->
    <field name="campain_id" />    <!-- many2one filed that have relationship with utm.campaign model -->
    <field name="source_id" />    <!-- many2one field that have relationship with utm.source model -->
    <field name="medium_id" />    <!-- many2one field that have relationship with utm.medium model -->
</group>
```
##### 3. mixin: website
```py
class ModelName(models.Model):  #  add a website visibility toggle on any of your record
    _name ="model.name"
    _inherit = ['website.mixin']      # add "website" in "deponds":[] in __manifest__

    def _compute_website_url(self):        # method for oweriding the website_url
        for rec in self:                     # use website_url in xml
            rec.website_url = "/mypath/%s" % (rec.id)
```
```xml
<button name="website_publish_button" type="object" icon="fa-globe" />   <!-- publish button for website in dashboard -->
    <field name="website_published" widget="website_button" />
</button>
<group>
    <field name="website_url" />           <!-- see website_url in form-view -->
</group>
```
###### 4. mixin: seo
```py
class ModelName(models.Model):
    _name ="model.name"                   # usefull for seo optimization
    _inherit = ['seo.mixin.metadata']      # add "seo" in "deponds":[] in __manifest__
```

### Mailing
we have diferenet models for mailing:
1. messages in the chatter comes from model: "mail.message". in `settting/Technical/Messages` which represent any message send or recive.
2. mail.mail in `setting/Technical/Emails` which inherits "mail.message" and its only for sending emails
3. with `outgoing email Servers` we can send messages out of odoo.
4. with `incoming email Servers` we can recieve from external serviecs

```py
class ModelName(models.Model):             # create an action for button of sending email
    _name ="model.name"                   # with "outgoing email server" we can send emails 
    def action_send_email(self):
        template = self.env.ref('moduleName.id_mail_template')
        template.send_mail(self.id, force_send=True)
# <button name='action_send_email' type='object' string='send email' calss='btn btn-primary' />
# use this button in form-view for sending email
```

```xml
<odoo>                  <!-- create a template for emails -->
    <data>
        <record id="id_mail_template" model="mail.template">             <!-- use this id in model ref -->
            <field name="name">Property Information</field>
            <field name="email_from">test@qa.team</field>                         <!-- static email -->
            <field name="email_from">{{object.user_email}}</field>                <!-- email of object -->
            <field name="email_to">{{object._get_emails()}}</field>               <!-- method that provide all emails -->
            <field name="model_id" ref="moduleName.model_id_with_no_dotnotation"/>    <!-- model_id in csv -->
            <field name="auto_delete" eval="True"/>          <!-- delete email after sending-->
            <field name="subject">New Information on {{object.name}}</field>     <!-- subject of email -->
            <field name="body_html" type="html">
                <p>
                    Dear Bidders,
                </p>
            </field>
        </record>
    </data>
</odoo>
```

### XML-RPC
+ RPC (Remote Procedure Call) for client-server communication.
+  XML-RPC is a Remote Procedure Call method that uses XML passed via HTTP(S) as a transport
+ we can read and manipulate data( in odoo) using XML-RPC package (py,java,nodejs) outside odoo framework.

```py
import xmlrpc.client                # python by default has a lib
url = 'http://localhost:8869'         # server ip
username = 'username'              #  user in odoo
password = 'password'            # we can connect with 'API KEY' or 'password'
db = 'db_name'                   # db of odoo

common_url = xmlrpc.client.ServerProxy(f'{url}/xmlrpc/2/common')     # odoo use /xmplrpc/2/common for connecting
print(common_url.version())                                       # with common_url we can find version of odoo
user_uid = common_url.authenticate(db, username, password, {})     # common_url is for authenticating 
# user_id is an integer [ we can see this id in dashboard]

models = xmlrpc.client.ServerProxy(f'{url}/xmlrpc/2/object')     # url for calling methods
# formula: 'db','uid', 'password' , 'modelName', 'method_name', [list of parameters passing to server], {list of fields returns from server}
# -------------------------------------------------------
# search function   --->search returns just ids of records
property_ids = models.execute_kw(db, user_uid, password, 'estate.property', 'search', [[]])    # empty array means search all
property_ids = models.execute_kw(db, user_uid, password, 'estate.property', 'search', [[]], {'offset': 0, 'limit': 10'})    # offset means jump

# count function
count_property_ids = models.execute_kw(db, user_uid, password, 'estate.property', 'search_count', [[]])

# read function   ----> return the whole object of records
read_property_ids = models.execute_kw(db, user_uid, password, 'estate.property', 'read', [property_ids])  # pass ids

# search and read function (same as above) --> recommend
search_read_property_ids = models.execute_kw(db, user_uid, password, 'estate.property', 'search_read', [[]], {'fields': ['name']})   # just show name field in each record
# create function (create new record)
create_property_id = models.execute_kw(db, user_uid, password, 'estate.property', 'create', [{'name': 'Property from RPC', 'sales_id': 6}])

# update function
write_property_id = models.execute_kw(db, user_uid, password, 'estate.property', 'write', [[8], {'name': 'Property from RPC Updated 2'}])  # return true
read_name_get = models.execute_kw(db, user_uid, password, 'estate.property', 'name_get', [[8]])

# unlink function
unlink_property_id = models.execute_kw(db, user_uid, password, 'estate.property', 'unlink', [[8]])
```
### Json-RPC

```py
import json
import random
import urllib.request            # python by default has a lib for json-rpc
odoo_url = 'http://localhost:8869'
username = 'username'
password = 'password'
db = 'db_name'

def json_rpc(url, method, params):
    data = {
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": random.randint(0, 1000000000)
    }
    headers = {
        "Content-Type": "application/json"
    }
    req = urllib.request.Request(url=url, data=json.dumps(data).encode(), headers=headers)  # url, data, headers
    response = json.loads(urllib.request.urlopen(req).read().decode("UTF-8"))
    if response.get('error'):
        raise Exception(response["error"])
    return response["result"]


def call(url, service, method, *args):
    return json_rpc(f"{url}/jsonrpc", "call", {"service": service, "method": method, "args": args})
#localhost:8869/jsonrpc

user_id = call(odoo_url, "common", "login", db, username, password)    # *args= [db, username, password]
print(user_id)    # user_id is an integer which we can see in dashboard like 2

vals = {
    "name": "Property from JSON",
    "sales_id": 6
}
# all the methods in XML-RPC are the same with JSON-RPC
create_property = call(odoo_url, "object", "execute", db, user_id, password, 'estate.property', 'create', vals)
print(create_property)

read_property = call(odoo_url, "object", "execute", db, user_id, password, 'estate.property', 'read', [9])
print(read_property)
```

### Odoo-RPC

```py

```

### Web Controller
```
|controller \
|--- main.py                    # import main.py into __init__
|--- __inti__.py               # import this file into root __init__
```

```py
from odoo import http
from odoo.http import request
class PropertyController(http.Controller):
    # auth="public" ,'user','none'             # type of req: http, json   # methods=['GET'], csrf=False, cors="*"
    @http.route(['/myurl'], type='http', website=True, auth="public")   # create a route: http://localhost:8069/properties
    def show_properties(self):
        model_ids = request.env['modelName'].sudo().search([])                   # by search we can get ids of all records
        return request.render("moduleName.id_template", {"model_ids": model_ids})   # render template with this dictionary
```

```xml
<odoo>                                               <!-- template for url: /myurl -->
    <template id="id_template" name="name_for_myurl">   <!-- t-call is for report templates and websites -->
        <t t-call="website.layout" t-cache="true">                    <!-- use this template in website layout -->
            <div class="oe_structure">                   <!-- html tags -->
                <div class="container">                  <!-- t-cache means cache in browser -->
                    <h3>My Table</h3>
                </div>
            </div>
                <table class="table-striped table">
                    <thead>
                        <tr>
                            <th>First Name</th>
                            <th>Last Name</th>
                        </tr>
                    </thead>
                    <tbody>
                        <t t-foreach="model_ids" t-as="obj">       <!-- for loop -->
                            <tr>
                                <th><span t-esc="obj.first_name"/></th>
                                <th><span t-esc="obj.last_name"/></th>
                            </tr>
                        </t>
                    </tbody>
                </table>
        </t>
    </template>
</odoo>
```

### Qweb
```xml
<!----------------------------------------- 1. if -------------------------->

<t t-if="condition">
    <!-- Bcz we used <t> tag as a parnet we should its mandatory to use html tags in this body -->
    <div> something </div>             <!-- acceptable --->
    a text without html tag             <!-- not acceptable -->
</t>

<div t-elif="other_condition">
    a text <!-- Alternative condition with no html tag in the body -->
</div>

<t t-else="">
    <!-- Default content -->
</t>

<!-------------------------------------------- 2. loop ----------------------->

<t t-foreach="records" t-as="record">
    <p t-esc="record.name"/>
    
    <!-- Available loop variables -->
    <span t-esc="record_index"/>      <!-- 0-based index -->
    <span t-esc="record_size"/>       <!-- Total items -->
    <span t-esc="record_first"/>      <!-- True for first item -->
    <span t-esc="record_last"/>       <!-- True for last item -->
    <span t-esc="record_parity"/>     <!-- "even" or "odd" -->
</t>

<!------------------------------------ 3. variable assienment ------------------------------>

<t t-set="variable_name" t-value="expression"/>
<t t-set="today" t-value="datetime.date.today()"/>

<!------------------------ 3. Output Escaping ----------------------------------------->

<p t-esc="user_input"/>            <!-- Safe -->
<div t-raw="trusted_html"/>          <!-- Potentially unsafe -->

<!-------------------------- 4. ternary operator ------------------------------------->

<div t-attf-class="container #{error ? 'bg-danger' : ''}">
    <!-- Generates class="container bg-danger" when error is true -->
</div>

<!--------------------------- 5. log a variable ------------------------------------------>

<t t-log="variable_name"/>

<!--------------------------------- 6. debug ------------------------------------------------------->

<t t-debug="">
    <!-- Breaks execution to debug template -->
</t>
```

### CSS

###### 1. Inline css
```xml
<div style="color: red; font-size: 16px;">
    This text will be red and 16px
</div>
```
###### 2. Style Tag
```xml
<template>
    <style>
        .report-title {
            color: aqua;
        }
    </style>
    <div> a text </div>
</template>
```

###### 3. Internal Link
```xml
<template>
    <link rel='stylesheet' href='modelName/static/src/css/style.css' />
    <div class='myclass' id='myid'> a text </div>
</template>
```
###### 4. using bundlers
with bundlers there is no need for using `<link rel="" />` tag, just use `<t t-call="website.layout">` as a parent element for `web controllers tempaltes`


###### 5. SCSS
+ with bundlers there is no need for using `<link rel="" />` tag, just use `<t t-call="website.layout">` as a parent element for `web controllers tempaltes`
+ make sure you installed 'sass'

### Bundles (Assests)
Odoo assets are grouped by bundles. Bundles are file path which we put a collection of assets into specfic folders. These file paths should introduce to `__manifest__` with `assets={}`

```
frontend           # website
```

### Javascript

###### 0. inline js

```xml
<template>
    <script> console.log("some thing") </script>
</template>
```

###### 1. Plain js(es5)
use bundles to run js in odoo
if you want to use external libraries like "axios" or dom manipulation, use plain js
plain javascript files do not offer the benefits of a module system

```js
(function () {              // use self invoking function to prevent from hoisting
  let a = 1;               // avoid leaking local variables to the global scope.
  console.log(a);
})();
```
###### 2. Native js module (es6 modules)
+ by default Odoo transpiles files under /static/src and /static/tests into Odoo modules. (In other folders, files aren’t transpiled by default)
+ for ES6 modules you should write `/** @odoo-module **/` at the first line of your code.
+ odoo has some limitation of useing native es6 modules. (check docs: "limitation")

```js
/** @odoo-module **/                              
import {something} from `./file_a`               // we can use es6 syntax
import {something} from `@module_name/file_a`  // for other moduels use fullname path
// we can use /** @odoo-module alias=moduleName.someName**/  for maping names if you want to use Native modules into odoo js modules
```

###### 3. Odoo js module
+ Odoo module: Odoo has defined a small module system located in the file addons/web/static/src/js/boot.js
```js
// there is no need for importing anything in js file to access odoo.define()
// there is no need for use <script src=''/> tag in the views, this script will run in every page
odoo.define('ModuleName.specificName',['moduleName.B'],function(require){            // odoo.define('name', ()=>{})
   "use strict"                 //  second argument is dependencies array
    var A = require('moduleName.A');      // specificName is a  service
})
```


### OWL
+ OWL(Odoo Web Library) is a component framework inspired by react (class based components with hooks).
+ OWL introduced in Odoo 13+ to replace the legacy Widget system (based on jQuery).
+ Doc: https://github.com/odoo/owl/blob/master/doc/readme.md
+ Discuss module in odoo adons use OWL
+ OWL has predefined components and we can use it in our project like: `ColorList`, `CheckBox`, `DropDown` and so on.

##### 0. owl structure

```
| moduleName\
|--- static\
|------ src\                      # load files in __manifest__ xp: 'web.assets_frontend'
|--------- main.js                # load main.js file at the end in __manifest__
|--------- components\              # import all xml files together, then js files together
|------------ component.xml        # load xml file first bcz in js file we use xml file
|------------ component.js
|------------ component.css
```
1. step1= component

```js
/** @odoo-module **/
import { Component, xml, useState,useRef,useEffect } from "@odoo/owl";
export class MyComponent extends Component {   // class component    // at the top of template <t t-name="myaddon.MyComponent"> 
    static template = 'myaddon.MyComponent';   // id of xml template    
    setup() {                                             //  initialize component here
        this.state = useState({ value: 'Hello' });           // <div t-esc="state.value" />
    }
    onButtonClick() {                                 // like event handler
        this.state.value = 'World';  // <button t-on-click="onButtonClick">Click Me</button>
    }
}
```
2. step2 = template of the class component

```xml
<?xml version="1.0" encoding="UTF-8" ?>     <!-- do not use <odoo> tag for owl templates --> 
<templates xml:space="preserve">          <!-- xml:space="preserve" for preserve white space(tabs, new lines) -->
<t t-name="myaddon.MyComponent" owl='1'>       <!-- name of class component --> 
    <button t-on-click="onButtonClick">Click Me</button>
    <div>
        <t t-esc="state.value"/>
    </div>
</t>
</templates>
```
3. step3 = mount your root component

```js
/** @odoo-module **/        // without this line you get this error: // The message port closed before a response was received.
import { mount, whenReady } from "@odoo/owl";
import { Navbar } from "../navbar/navbar";  //relative path is ok
import { templates } from "@web/core/assets";      // you do not neet to import anything in view template

owl.whenReady( () => {    // app is an id of an element in the view tempalate
  mount(Navbar, document.getElementById("app"), { templates, dev: false });  // alternative: document.body
});
```

###### 1. OWL: Lifecycle Hooks


```js
export class OwlTodoList extends Component {
    setup() {      // 1. `setup()`       ----->	Runs once when the component initializes
        this.state = useState({ data: null });
    }
    async willStart() {   // 2. `willStart()`	 ----->Async setup before rendering (e.g., fetch data)
        this.state.data = await this.fetchData();
    }
    mounted() {    //3. `mounted()`	----->Runs after the component is inserted into DOM
        console.log("Component is in the DOM!");
    }
    willUnmount() {  // 4. `willUnmount()`	-----> Runs before removal (cleanup event listeners)
        console.log("Component is being removed");
    }
}
```
##### 2. OWL: UseEffect
```js
setup() {
    this.state = useState({ value: 1,text:"def"});
    useEffect(
        () => {console.log("log");},
        () => [this.state]
    );
}
```
##### 3. OWL: Events
```js
export class Body extends Component{
    static template = 'real_state_ads.Body';
    _updateInputValue(event) {
        // <input t-on-input="_updateInputValue" />
        this.state.text = event.target.value;
    }
    increment(){  
        // <button t-on-click="increment">
      this.state.value= this.state.value+1;
    }
}
```


### Registery
+ Registries are (ordered) key/value maps


### widgets
https://www.cybrosys.com/blog/widgets-list-in-odoo-14
https://www.cybrosys.com/odoo/odoo-books/odoo-17-development/views/widgets/









### Tools
https://github.com/Yenthe666/InstallScript
https://github.com/vertelab/odootools











