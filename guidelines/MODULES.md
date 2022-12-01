
# QubiQ Code Guidelines (2022/02/05)


- [QubiQ Code Guidelines (2022/02/05)](#qubiq-code-guidelines-20220205)
- [Modules](#modules)
  - [Common Specification](#common-specification)
  - [Customer Modules](#customer-modules)
  - [Transversal Modules](#transversal-modules)
  - [Third party Modules](#third-party-modules)
- [Version numbers](#version-numbers)
- [Migrations](#migrations)
- [Directories](#directories)
- [File naming](#file-naming)
- [Installation hooks](#installation-hooks)
- [Complete structure](#complete-structure)
- [Format](#format)
- [Naming xml_id](#naming-xml_id)
  - [Data Records](#data-records)
  - [Security, View and Action](#security-view-and-action)
  - [Inherited XML](#inherited-xml)
- [PEP8 options](#pep8-options)
- [Symbols](#symbols)
  - [Odoo Python Classes](#odoo-python-classes)
  - [Variable names](#variable-names)
- [SQL](#sql)
  - [No SQL Injection](#no-sql-injection)
  - [Never commit the transaction](#never-commit-the-transaction)
- [Do not bypass the ORM](#do-not-bypass-the-orm)
- [Models](#models)
- [Fields](#fields)
- [Exceptions](#exceptions)
- [Commit message](#commit-message)


************
Introduction
************

This page introduces the coding guidelines for projects hosted under QubiQ. These
guidelines aim to improve the quality of the code: better readability of
source, better maintainability, better stability and fewer regressions.

These are loosely based on the `OCA Guidelines <https://github.com/OCA/odoo-community.org/blob/master/website/Contribution/CONTRIBUTING.rst>`_ but not as extense as the original one


Modules
=======

Common Specification
--------------------

* Use of the singular form in module name (or use "multi"),
  except when compound of module name or object Odoo
  that is already in the plural (i.e. `mrp_operations_...`).

* In `__manifest__.py`:

  * Avoid empty keys
  * Make sure it has the `license` and `images` keys.
  * Make sure the text `,Qubiq 2010 ` is appended to the
    `author` text.
  * The `website` key must be `https://www.qubiq.es`.
   
Customer Modules
----------------

* All customer modules must be placed in the private folder of the project 
* Use the follwing sintax: <CUSTOMER_PREFIX>_<ODOO_MODULE>_<FEATURE>. For I.e: gl_sale_integration
* Use a the same customer icon for all the modules
* When extending an Odoo module, prefix yours with that module's name. I.e.
  gl_mail_forward.

Transversal Modules
-------------------

* Transversal modules will be placed in the public repo of Qubiq (github)
* Use the following syntax: qu_<ODOO_MODULE>_<FEATURE>. For I.e: qu_mis_builder_comment
* Read the following guide to colaborate on the public QubiQ repo : XXXX

Third party Modules
-------------------

* All the third party modules must be located in a specific folder for avoiding pipelines or code formatting.



Version numbers
===============

The version number in the module manifest should be the Odoo major
version (e.g. `12.0`) followed by the module `x.y.z` version numbers.
For example: `12.0.1.0.0` is expected for the first release of an 12.0
module.

The `x.y.z` version numbers follow the semantics `breaking.feature.fix`:

* `x` increments when the data model or the views had significant
  changes. Data migration might be needed, or depending modules might be affected.
* `y` increments when non-breaking new features are added. A module
  upgrade will probably be needed.
* `z` increments when bugfixes were made. Usually a server restart
  is needed for the fixes to be made available.

If applicable, breaking changes are expected to include instructions
or scripts to perform migration on current installations.

Migrations
==========

When you introduce a breaking change, you *must* provide a migration script to make it possible to upgrade from lower versions. For a migration to another major version of Odoo, it's quite probable you'll need a migration script too. In such cases, migration scripts are highly appreciated, but a note in the README about relevant changes needing migration is sufficient too so that later contributors can add migration scripts without having to analyze all changes again.

For forward porting a module, consult: https://github.com/OCA/maintainer-tools/wiki#migration

Directories
===========

A module is organized in a few directories:

* `controllers/`: contains controllers (http routes)
* `data/`: data xml
* `demo/`: demo xml
* `examples/`: external files
  `lib/`, ...
* `models/`: model definitions
* `report/`: reporting models (BI/analysis), Webkit/RML print report templates
* `static/`: contains the web assets, separated into `css/`, `js/`, `img/`,
* `templates/`: if you have several web templates and several backend views you can split them here
* `views/`: contains the views and templates, and QWeb report print templates
* `wizards/`: wizard model and views


File naming
===========

For `models`, `views` and `data` declarations, split files by the model
involved, either created or inherited. When they are XML files, a suffix should
be included with its category. For example, demo data for res.partner should go
in a file named `demo/res_partner_demo.xml` and a view for partner should go in
a file named `views/res_partner_views.xml`. An exception can be made when the
model is a model intended to be used only as a one2many model nested on the
main model. In this case, you can include the model definition inside it.
Example `sale.order.line` model can be together with `sale.order` in
the file `models/sale_order.py`.

For model named `<main_model>` the following files may be created:

* `models/<main_model>.py`
* `data/<main_model>_data.xml`
* `demo/<main_model>_demo.xml`
* `templates/<main_model>_template.xml`
* `views/<main_model>_views.xml`

For `controller`, if there is only one file it should be named `main.py`.
If there are several controller classes or functions you can split them into
several files.

For `static files`, the name pattern is `<module_name>.ext` (i.e.
`static/js/im_chat.js`, `static/css/im_chat.css`, `static/xml/im_chat.xml`,
...). Don't link data (image, libraries) outside Odoo: don't use an url to an
image but copy it in our codebase instead.

Installation hooks
==================

When **`pre_init_hook`**, **`post_init_hook`**, **`uninstall_hook`**
and **`post_load`** are
used, they should be placed in **`hooks.py`** located at the root of module
directory structure and keys in the manifest file keeps the same as the
following

.. code-block:: python

    {
        'pre_init_hook': 'pre_init_hook',
        'post_init_hook': 'post_init_hook',
        'uninstall_hook': 'uninstall_hook',
        'post_load': 'post_load',
    }

Remember to add into the **`__init__.py`** the following imports as
needed. For example:

.. code-block:: python

    from .hooks import pre_init_hook, post_init_hook, uninstall_hook, post_load

For applying monkey patches use post_load hook.
In order to apply them just if the module is installed.

Complete structure
==================

The complete tree should look like this:

.. code-block::

    addons/<my_module_name>/
    |-- __init__.py
    |-- __manifest__.py
    |-- hooks.py
    |-- controllers/
    |   |-- __init__.py
    |   `-- main.py
    |-- data/
    |   `-- <main_model>.xml
    |-- demo/
    |   `-- <inherited_model>.xml
    |-- migrations/
    |   `-- 12.0.x.y.z/
    |       |-- pre_migration.py
    |       `-- post_migration.py
    |-- models/
    |   |-- __init__.py
    |   |-- <main_model>.py
    |   `-- <inherited_model>.py
    |-- report/
    |   |-- __init__.py
    |   |-- report.xml
    |   |-- <bi_reporting_model>.py
    |   |-- report_<rml_report_name>.rml
    |   |-- report_<rml_report_name>.py
    |   `-- <webkit_report_name>.mako
    |-- security/
    |   |-- ir.model.access.csv
    |   `-- <main_model>_security.xml
    |-- static/
    |   |-- img/
    |   |   |-- my_little_kitten.png
    |   |   `-- troll.jpg
    |   |-- lib/
    |   |   `-- external_lib/
    |   `-- src/
    |       |-- js/
    |       |   `-- <my_module_name>.js
    |       |-- css/
    |       |   `-- <my_module_name>.css
    |       |-- less/
    |       |   `-- <my_module_name>.less
    |       `-- xml/
    |           `-- <my_module_name>.xml
    |-- tests/
    |   |-- __init__.py
    |   |-- <test_file>.py
    |   `-- <test_file>.yml
    |-- views/
    |   |-- <main_model>_views.xml
    |   |-- <inherited_main_model>_views.xml
    |   `-- report_<qweb_report>.xml
    |-- templates/
    |   |-- <main_model>.xml
    |   `-- <inherited_main_model>.xml
    |-- wizards/
    |   |-- __init__.py
    |   |-- <wizard_model>.py
    |   `-- <wizard_model>.xml
    `-- examples/
        `-- my_example.csv

Filenames should use only `[a-z0-9_]`

Use correct file permissions: folders 755 and files 644.

*********
XML files
*********

Format
======

When declaring a record in XML:

* Indent using four spaces
* Place `id` attribute before `model`
* For field declarations, the `name` attribute is first. Then place the `value`
  either in the `field` tag, either in the `eval` attribute, and finally other
  attributes (widget, options, ...) ordered by importance.
* Try to group the records by model. In case of dependencies between
  action/menu/views, the convention may not be applicable.
* Use naming convention defined at the next point
* The tag `<data>` is only used to set not-updatable data with `noupdate=1`
  when your data file contains a mix of "noupdate" data. Otherwise, you should
  use one of these:

  - `<odoo>`: for `noupdate=0` or demo data (demo data is non-updatable by default)
  - `<odoo noupdate='1'>`

* Do not prefix the xmlid by the current module's name
  (`<record id="view_id"...`, not `<record id="current_module.view_id"...`)


.. code-block:: xml

    <record id="view_id" model="ir.ui.view">
        <field name="name">view.name</field>
        <field name="model">object_name</field>
        <field name="priority" eval="16"/>
        <field name="arch" type="xml">
            <tree>
                <field name="my_field_1"/>
                <field name="my_field_2" string="My Label" widget="statusbar" statusbar_visible="draft,sent,progress,done" statusbar_colors='{"invoice_except":"red","waiting_date":"blue"}' />
            </tree>
        </field>
    </record>


Naming xml_id
=============

Data Records
------------

Use the followng pattern, where `<model_name>` is the name of the model that
the record is an instance of: `<model_name>_<record_name>`

.. code-block:: xml

    <record id="res_users_important_person" model="res.users">
        ...
    </record>

Security, View and Action
-------------------------

Use the following patterns, where `<model_name>` is the name of the model that
the menu, view, etc. belongs to (e.g. for a `res.users` form view, the name
would be `res_users_view_form`):

* For a menu: `<model_name>_menu`
* For a view: `<model_name>_view_<view_type>`, where `view_type` is kanban,
  form, tree, search, ...
* For an action: the main action respects `<model_name>_action`. Others are
  suffixed with `_<detail>`, where `detail` is an underscore lowercase string
  explaining the action (should not be long). This is used only if
  multiple actions are declared for the model.
* For a group: `<model_name>_group_<group_name>` where `group_name` is the
  name of the group, generally 'user', 'manager', ...
* For a rule: `<model_name>_rule_<concerned_group>` where `concerned_group` is
  the short name of the concerned group ('user' for the
  'model_name_group_user', 'public' for public user, 'company' for
  multi-company rules, ...).

.. code-block:: xml

    <!-- views and menus -->
    <record id="model_name_menu" model="ir.ui.menu">
        ...
    </record>

    <record id="model_name_view_form" model="ir.ui.view">
        ...
    </record>

    <record id="model_name_view_kanban" model="ir.ui.view">
        ...
    </record>

    <!-- actions -->
    <record id="model_name_action" model="ir.actions.act_window">
        ...
    </record>

    <record id="model_name_action_child_list" model="ir.actions.act_window">
        ...
    </record>

    <!-- security -->
    <record id="model_name_group_user" model="res.groups">
        ...
    </record>

    <record id="model_name_rule_public" model="ir.rule">
        ...
    </record>

    <record id="model_name_rule_company" model="ir.rule">
        ...
    </record>

Inherited XML
-------------

A module can extend a view only one time.

The naming rules should be followed even when a view is inherited, the module
name prevents xid conflicts. In the case where an inherited view has a name
which does not follow the guidelines set above, prefer naming the inherited
view after the original over using a name which follows the guidelines. This
eases looking up the original view and other inheritance if they all have the
same name.


.. code-block:: xml

    <record id="original_id" model="ir.ui.view">
        <field name="inherit_id" ref="original_module.original_id"/>
        ...
    </record>

Use of `<... position="replace">` is not recommended because
could show the error `Element ... cannot be located in parent view`
from other inherited views with this field.

If you need to use this option, it must have an explicit comment
explaining why it is absolutely necessary and also use a
high value in its `priority` (greater than 100 is recommended) to avoid the error.

.. code-block:: xml

    <record id="view_id" model="ir.ui.view">
        <field name="name">view.name</field>
        <field name="model">object_name</field>
        <field name="priority">110</field> <!--Priority greater than 100-->
        <field name="arch" type="xml">
            <!-- It is necessary because...-->
            <xpath expr="//field[@name='my_field_1']" position="replace"/>
        </field>
    </record>

Also, we can hide an element from the view using `invisible="1"`.


******
Python
******

PEP8 options
============

Using the linter flake8 can help to see syntax and semantic warnings or errors.
Project Source Code should adhere to PEP8 and PyFlakes standards with
a few exceptions:

* In `__init__.py` only

  *  F401: `module` imported but unused


Symbols
=======

Odoo Python Classes
-------------------

Use UpperCamelCase for code in api 


.. code-block:: python

    class AccountInvoice(models.Model):
        ...

  

Variable names
--------------

* Always give your variables a meaningful name. You may know what it's
  referring to now, but you won't in 2 months, and others don't either.
  One-letter variables are acceptable only in lambda expressions and loop
  indices, or perhaps in pure maths expressions (and even there it doesn't hurt
  to use a real name).

.. code-block:: python

    # unclear and misleading
    a = {}
    sfields = {}

    # better
    results = {}
    selected_fields = {}

* Use underscore lowercase notation for common variables (snake_case)
* Since new API works with records or recordsets instead of id lists, don't
  suffix variable names with `_id` or `_ids` if they do not contain an ids or
  lists of ids.

  .. code-block:: python

    res_partner = self.env['res.partner']
    partners = res_partner.browse(ids)
    partner_id = partners[0].id

* Use underscore uppercase notation for global variables or constants

  .. code-block:: python

    CONSTANT_VAR1 = 'Value'
    ...
    class ...
    ...


SQL
===

No SQL Injection
----------------

Care must be taken not to introduce SQL injections vulnerabilities when using manual SQL queries. The vulnerability is present when user input is either incorrectly filtered or badly quoted, allowing an attacker to introduce undesirable clauses to a SQL query (such as circumventing filters or executing **UPDATE** or **DELETE** commands).

The best way to be safe is to never, NEVER use Python string concatenation (+) or string parameters interpolation (%) to pass variables to a SQL query string.

The second reason, which is almost as important, is that it is the job of the database abstraction layer (psycopg2) to decide how to format query parameters, not your job! For example psycopg2 knows that when you pass a list of values it needs to format them as a comma-separated list, enclosed in parentheses!

.. code-block:: python

    # the following is very bad:
    #   - it's a SQL injection vulnerability
    #   - it's unreadable
    #   - it's not your job to format the list of ids
    cr.execute('select distinct child_id from account_account_consol_rel ' +
               'where parent_id in ('+','.join(map(str, ids))+')')

    # better
    cr.execute('SELECT DISTINCT child_id '\
               'FROM account_account_consol_rel '\
               'WHERE parent_id IN %s',
               (tuple(ids),))

This is very important, so please be careful also when refactoring, and most importantly do not copy these patterns!

Here is a
`memorable example <http://www.bobby-tables.com>`_
to help you remember what the issue is about (but do not copy the code there).

Before continuing, please be sure to read the online documentation of pyscopg2 to learn of to use it properly:

- `The problem with query parameters <http://initd.org/psycopg/docs/usage.html#the-problem-with-the-query-parameters>`_
- `How to pass parameters with psycopg2 <http://initd.org/psycopg/docs/usage.html#passing-parameters-to-sql-queries>`_
- `Advanced parameter types <http://initd.org/psycopg/docs/usage.html#adaptation-of-python-values-to-sql-types>`_

Never commit the transaction
----------------------------

The Odoo framework is in charge of providing the transactional context for all
RPC calls.
The principle is that a new database cursor is opened at the beginning of each
RPC call, and committed when the call has returned, just before transmitting the
answer to the RPC client, approximately like this:

.. code-block:: python

    def execute(self, db_name, uid, obj, method, *args, **kw):
        db, pool = pooler.get_db_and_pool(db_name)
        # create transaction cursor
        cr = db.cursor()
        try:
            res = pool.execute_cr(cr, uid, obj, method, *args, **kw)
            cr.commit() # all good, we commit
        except Exception:
            cr.rollback() # error, rollback everything atomically
            raise
        finally:
            cr.close() # always close cursor opened manually
        return res

If any error occurs during the execution of the RPC call, the transaction is rolled back atomically, preserving the state of the system.

Similarly, the system also provides a dedicated transaction during the execution of tests suites, so it can be rolled back or not depending on the server startup options.

The consequence is that if you manually call `cr.commit()` anywhere there is a very high chance that you will break the system in various ways, because you will cause partial commits, and thus partial and unclean rollbacks, causing among others:

- inconsistent business data, usually data loss ;
- workflow desynchronization, documents stuck permanently ;
- tests that can't be rolled back cleanly, and will start polluting the database, and triggering error (this is true even if no error occurs during the transaction);

Unless:

- You have created your own database cursor explicitly! And the situations where you need to do that are exceptional!
  And by the way if you did create your own cursor, then you need to handle error cases and proper rollback, as well as properly close the cursor when you're done with it.

  And contrary to popular belief, you do not even need to call `cr.commit()` in the following situations:

  - in the `_auto_init()` method of an `models.Model` object: this is taken care of by the addons initialization method, or by the ORM transaction when creating custom models
  - in reports: the `commit()` is handled by the framework too, so you can update the database even from within a report
  - within `models.TransientModel` methods: these methods are called exactly like regular `models.Model` ones, within a transaction and with the corresponding `cr.commit()`/`rollback()` at the end ;
  - etc. (see general rule above if you have in doubt!)

- All `cr.commit()` calls outside of the server framework from now on must have an explicit comment explaining why they are absolutely necessary, why they are indeed correct, and why they do not break the transactions. Otherwise they can and will be removed!

- You can avoid the `cr.commit` using `cr.savepoint` method.

  .. code-block:: python

        try:
            with cr.savepoint():
                # Create a savepoint and rollback this section if any exception is raised.
                method1()
                method2()
        # Catch here any exceptions if you need to.
        except (except_class1, except_class2):
            # Add here the logic if anything fails. NOTE: Don't need rollback sentence.
            pass

- You can isolate a transaction for a valid `cr.commit` using `Environment`:

  .. code-block:: python

        with odoo.api.Environment.manage():
            with odoo.registry(self.env.cr.dbname).cursor() as new_cr:
                # Create a new environment with new cursor database
                new_env = api.Environment(new_cr, self.env.uid, self.env.context)
                # with_env replace original env for this method
                # A good comment here of why this isolated transaction is required.
                self.with_env(new_env).write({'name': 'hello'})  # isolated transaction to commit
            # You don't need to close nor to commit your cursor as they are done when exiting "with" block
        # You don't need clear caches because is cleared when finish "with"

Do not bypass the ORM
=====================

You should never use the database cursor directly when the ORM can do the same
thing! By doing so you are bypassing all the ORM features, possibly the
transactions, access rights and so on.

And chances are that you are also making the code harder to read and probably
less secure (see also previous guideline: `No SQL Injection`_):

.. code-block:: python

    # very very wrong
    cr.execute('select id from auction_lots where auction_id in (' +
               ','.join(map(str, ids)) + ') and state=%s and obj_price>0',
               ('draft',))
    auction_lots_ids = [x[0] for x in cr.fetchall()]

    # no injection, but still wrong
    cr.execute('select id from auction_lots where auction_id in %s '
               'and state=%s and obj_price>0',
               (tuple(ids), 'draft',))
    auction_lots_ids = [x[0] for x in cr.fetchall()]

    # better
    auction_lots_ids = self.search(cr, uid, [
        ('auction_id', 'in', ids),
        ('state', '=', 'draft'),
        ('obj_price', '>', 0),
    ])

Models
======

* Model names

  * Use dot lowercase name for models. Example: `sale.order`
  * Use name in a singular form. `sale.order` instead of `sale.orders`

* Method conventions

  * Compute Field: the compute method pattern is `_compute_<field_name>`
  * Inverse method: the inverse method pattern is `_inverse_<field_name>`
  * Search method: the search method pattern is `_search_<field_name>`
  * Default method: the default method pattern is `_default_<field_name>`
  * Onchange method: the onchange method pattern is `_onchange_<field_name>`
  * Constraint method: the constraint method pattern is
    `_check_<constraint_name>`
  * Action method: an object action method is prefix with `action_`.
    Its decorator is `@api.multi`, but since it use only one record, add
    `self.ensure_one()` at the beginning of the method.

* In a Model attribute order should be

  #. Private attributes (`_name`, `_description`, `_inherit`, ...)
  #. Fields declarations
  #. SQL constraints
  #. Default method and `_default_get`
  #. Compute and search methods in the same order than field declaration
  #. Constrains methods (`@api.constrains`) and onchange methods
     (`@api.onchange`)
  #. CRUD methods (ORM overrides)
  #. Action methods
  #. And finally, other business methods.

.. code-block:: python

    class Event(models.Model):
        # Private attributes
        _name = 'event.event'
        _description = 'Event'

        # Fields declaration
        name = fields.Char(default=lambda self: self._default_name())
        seats_reserved = fields.Integer(
            oldname='register_current',
            string='Reserved Seats',
            store=True,
            readonly=True,
            compute='_compute_seats',
        )
        seats_available = fields.Integer(
            oldname='register_avail',
            string='Available Seats',
            store=True,
            readonly=True,
            compute='_compute_seats',
        )
        price = fields.Integer(string='Price')

        # SQL constraints
        _sql_constraints = [
            ('name_uniq', 'unique(name)', 'Name must be unique'),
        ]

        # Default methods
        def _default_name(self):
            ...

        # compute and search fields, in the same order that fields declaration
        @api.multi
        @api.depends('seats_max', 'registration_ids.state')
        def _compute_seats(self):
            ...

        # Constraints and onchanges
        @api.constrains('seats_max', 'seats_available')
        def _check_seats_limit(self):
            ...

        @api.onchange('date_begin')
        def _onchange_date_begin(self):
            ...

        # CRUD methods
        def create(self):
            ...

        # Action methods
        @api.multi
        def action_validate(self):
            self.ensure_one()
            ...

        # Business methods
        def mail_user_confirm(self):
            ...

Fields
======

* `One2Many` and `Many2Many` fields should always have `_ids` as suffix
  (example: sale_order_line_ids)
* `Many2One` fields should have `_id` as suffix
  (example: partner_id, user_id, ...)
* If the technical name of the field (the variable name) is the same to the
  string of the label, don't put `string` parameter for new API fields, because
  it's automatically taken. If your variable name contains "_" in the name,
  they are converted to spaces when creating the automatic string and each word
  is capitalized.
  (example:

      old api `'name': fields.char('Name', ...)`
      new api `'name': fields.Char(...)`)

* Default functions should be declared with a lambda call on self. The reason
  for this is so a default function can be inherited. Assigning a function
  pointer directly to the `default` parameter does not allow for inheritance.

  .. code-block:: python

      a_field(..., default=lambda self: self._default_get())

Exceptions
==========

The `pass` into block except is not a good practice!

By including the `pass` we assume that our algorithm can continue to function
after the exception occurred

If you really need to use the `pass` consider logging that exception

.. code-block:: python

    try:
        sentences
    except Exception:
        _logger.debug('Why the exception is safe....', exc_info=1))

**********
Javascript
**********

* `use strict;` is recommended for all javascript files
* Use `ESLint <https://eslint.org/>`_ with `this configuration
  <https://github.com/OCA/pylint-odoo/blob/master/pylint_odoo/examples/.jslintrc>`__
* Never add minified Javascript libraries
* Use UpperCamelCase for class declarations

***
CSS
***

* Prefix all your classes with `o_<module_name>` where `module_name` is the
  technical name of the module (`sale`, `im_chat`, ...) or the main route
  reserved by the module (for website module mainly,
  i.e. `o_forum` for website_forum module). The only exception for this rule is
  the webclient: it simply use `o_` prefix.
* Avoid using ids
* Use bootstrap native classes
* Use underscore lowercase notation to name classes 
  

*****
Tests
*****

As a general rule, a bug fix should come with a unittest which would fail
without the fix itself. This is to assure that regression will not happen in
the future. It also is a good way to show that the fix works in all cases.

New modules or additions should ideally test all the functions defined. The
coveralls utility will comment on pull requests indicating if coverage
increased or decreased. If it has decreased, this is usually a sign that a test
should be added. The coveralls web interface can also show which lines need
test cases.

**NOTE:** if you add an example module to showcase modules' features
you should name it ``module_name_example`` (ie: `cms_form` and `cms_form_example`).
In this way coverage analysis will ignore this extra module by default.

***
Git
***

Commit message
==============

Write a short commit summary without prefixing it. It should not be longer than
50 characters: `This is a commit message`

Commits messages are in English

The commit message structure **[TAG][TASK/ISSUE ID] description**
We are going to use the following tags:

* [FIX] for bug fixes: mostly used in stable version but also valid if you are fixing a recent bug in development version
* [REF] for refactoring: when a feature is heavily rewritten
* [ADD] for adding new modules;
* [REM] for removing resources: removing dead code, removing views, removing modules, …;
* [REV] for reverting commits: if a commit causes issues or is not wanted reverting it is done using this tag;
* [MOV] for moving files: use git move and do not change content of moved file otherwise Git may loose track and history of the file; also used when moving code from one file to another;
* [IMP] for improvements: most of the changes done in development version are incremental improvements not related to another tag;
* [I18N] for changes in translation files;

For task/issue ID we are going to use T-id_number for tasks and I-id_number for issues


