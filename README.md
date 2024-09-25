### Step 1: Create the Module Structure
First, let's create the necessary directory structure for our module:

```bash
app/code/LandingPage/Form/
├── Controller
│   └── Adminhtml
│       └── Index
│           └── Index.php
├── etc
│   ├── adminhtml
│   │   ├── menu.xml
│   │   └── routes.xml
│   ├── di.xml
│   └── module.xml
├── Model
│   └── ResourceModel
│       └── Form
│           └── Collection.php
├── Ui
│   ├── Component
│   │   └── Listing
│   │       └── Column
│   │           └── Actions.php
│   └── DataProvider
│       └── Form
│           └── ListingDataProvider.php
├── view
│   └── adminhtml
│       ├── layout
│       │   └── landingpage_form_index_index.xml
│       └── ui_component
│           └── landingpage_form_listing.xml
└── registration.php
```

### Step 2: Create the `module.xml` File
In `app/code/LandingPage/Form/etc/module.xml`:

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="LandingPage_Form" setup_version="1.0.0">
        <sequence>
            <module name="Magento_Backend"/>
            <module name="Magento_Ui"/>
        </sequence>
    </module>
</config>
```

This file declares our module and its dependencies.

### Step 3: Create the `registration.php` File
In `app/code/LandingPage/Form/registration.php`:

```php
<?php
use Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::MODULE,
    'LandingPage_Form',
    __DIR__
);
```

This file registers our module with Magento.

### Step 4: Create the Admin Menu
In `app/code/LandingPage/Form/etc/adminhtml/menu.xml`:

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Backend:etc/menu.xsd">
    <menu>
        <add id="LandingPage_Form::landingpage" title="Landing Pages" module="LandingPage_Form" sortOrder="50" resource="LandingPage_Form::landingpage"/>
        <add id="LandingPage_Form::form_list" title="Form List" module="LandingPage_Form" sortOrder="10" action="landingpage_form/index/index" resource="LandingPage_Form::form_list" parent="LandingPage_Form::landingpage"/>
    </menu>
</config>
```

This file adds our module to the admin menu.

### Step 5: Create the Admin Routes
In `app/code/LandingPage/Form/etc/adminhtml/routes.xml`:
```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:App/etc/routes.xsd">
    <router id="admin">
        <route id="landingpage_form" frontName="landingpage_form">
            <module name="LandingPage_Form" before="Magento_Backend"/>
        </route>
    </router>
</config>
```

This file defines the admin route for our module.

### Step 6: Create the Admin Controller
In `app/code/LandingPage/Form/Controller/Adminhtml/Index/Index.php`:

```php
<?php
namespace LandingPage\Form\Controller\Adminhtml\Index;

use Magento\Backend\App\Action;
use Magento\Backend\App\Action\Context;
use Magento\Framework\View\Result\PageFactory;

class Index extends Action
{
    protected $resultPageFactory;

    public function __construct(
        Context $context,
        PageFactory $resultPageFactory
    ) {
        parent::__construct($context);
        $this->resultPageFactory = $resultPageFactory;
    }

    public function execute()
    {
        $resultPage = $this->resultPageFactory->create();
        $resultPage->getConfig()->getTitle()->prepend(__('Landing Page Forms'));
        return $resultPage;
    }

    protected function _isAllowed()
    {
        return $this->_authorization->isAllowed('LandingPage_Form::form_list');
    }
}
```

This controller handles the main page of our admin grid.

### Step 7: Create the UI Component XML
In `app/code/LandingPage/Form/view/adminhtml/ui_component/landingpage_form_listing.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<listing xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Ui:etc/ui_configuration.xsd">
    <argument name="data" xsi:type="array">
        <item name="js_config" xsi:type="array">
            <item name="provider" xsi:type="string">landingpage_form_listing.landingpage_form_listing_data_source</item>
            <item name="deps" xsi:type="string">landingpage_form_listing.landingpage_form_listing_data_source</item>
        </item>
        <item name="spinner" xsi:type="string">landingpage_form_columns</item>
    </argument>
    <dataSource name="landingpage_form_listing_data_source">
        <argument name="dataProvider" xsi:type="configurableObject">
            <argument name="class" xsi:type="string">LandingPage\Form\Ui\DataProvider\Form\ListingDataProvider</argument>
            <argument name="name" xsi:type="string">landingpage_form_listing_data_source</argument>
            <argument name="primaryFieldName" xsi:type="string">entity_id</argument>
            <argument name="requestFieldName" xsi:type="string">id</argument>
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="component" xsi:type="string">Magento_Ui/js/grid/provider</item>
                    <item name="update_url" xsi:type="url" path="mui/index/render"/>
                    <item name="storageConfig" xsi:type="array">
                        <item name="indexField" xsi:type="string">entity_id</item>
                    </item>
                </item>
            </argument>
        </argument>
    </dataSource>
    <columns name="landingpage_form_columns">
        <selectionsColumn name="ids">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="resizeEnabled" xsi:type="boolean">false</item>
                    <item name="resizeDefaultWidth" xsi:type="string">55</item>
                    <item name="indexField" xsi:type="string">entity_id</item>
                </item>
            </argument>
        </selectionsColumn>
        <column name="entity_id">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="filter" xsi:type="string">textRange</item>
                    <item name="sorting" xsi:type="string">asc</item>
                    <item name="label" xsi:type="string" translate="true">ID</item>
                </item>
            </argument>
        </column>
        <column name="name">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="filter" xsi:type="string">text</item>
                    <item name="editor" xsi:type="array">
                        <item name="editorType" xsi:type="string">text</item>
                        <item name="validation" xsi:type="array">
                            <item name="required-entry" xsi:type="boolean">true</item>
                        </item>
                    </item>
                    <item name="label" xsi:type="string" translate="true">Name</item>
                </item>
            </argument>
        </column>
        <column name="email">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="filter" xsi:type="string">text</item>
                    <item name="editor" xsi:type="array">
                        <item name="editorType" xsi:type="string">text</item>
                        <item name="validation" xsi:type="array">
                            <item name="required-entry" xsi:type="boolean">true</item>
                            <item name="validate-email" xsi:type="boolean">true</item>
                        </item>
                    </item>
                    <item name="label" xsi:type="string" translate="true">Email</item>
                </item>
            </argument>
        </column>
        <actionsColumn name="actions" class="LandingPage\Form\Ui\Component\Listing\Column\Actions">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="resizeEnabled" xsi:type="boolean">false</item>
                    <item name="resizeDefaultWidth" xsi:type="string">107</item>
                    <item name="indexField" xsi:type="string">entity_id</item>
                </item>
            </argument>
        </actionsColumn>
    </columns>
</listing>
```

This file defines the structure and behavior of our admin grid.

### Step 8: Create the Layout XML
In `app/code/LandingPage/Form/view/adminhtml/layout/landingpage_form_index_index.xml`:

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <update handle="styles"/>
    <body>
        <referenceContainer name="content">
            <uiComponent name="landingpage_form_listing"/>
        </referenceContainer>
    </body>
</page>
```

This file includes our UI component in the admin page.

### Step 9: Create the Data Provider
In `app/code/LandingPage/Form/Ui/DataProvider/Form/ListingDataProvider.php`:

```php
<?php
namespace LandingPage\Form\Ui\DataProvider\Form;

use Magento\Ui\DataProvider\AbstractDataProvider;
use LandingPage\Form\Model\ResourceModel\Form\Collection;

class ListingDataProvider extends AbstractDataProvider
{
    public function __construct(
        $name,
        $primaryFieldName,
        $requestFieldName,
        Collection $collection,
        array $meta = [],
        array $data = []
    ) {
        parent::__construct($name, $primaryFieldName, $requestFieldName, $meta, $data);
        $this->collection = $collection;
    }

    public function getData()
    {
        if (!$this->getCollection()->isLoaded()) {
            $this->getCollection()->load();
        }
        $items = $this->getCollection()->toArray();
        return [
            'totalRecords' => $this->getCollection()->getSize(),
            'items' => array_values($items),
        ];
    }
}
```

This class provides data for our admin grid.

### Step 10: Create the Actions Column
In `app/code/LandingPage/Form/Ui/Component/Listing/Column/Actions.php`:

```php
<?php
namespace LandingPage\Form\Ui\Component\Listing\Column;

use Magento\Framework\View\Element\UiComponent\ContextInterface;
use Magento\Framework\View\Element\UiComponentFactory;
use Magento\Ui\Component\Listing\Columns\Column;
use Magento\Framework\UrlInterface;

class Actions extends Column
{
    protected $urlBuilder;

    public function __construct(
        ContextInterface $context,
        UiComponentFactory $uiComponentFactory,
        UrlInterface $urlBuilder,
        array $components = [],
        array $data = []
    ) {
        $this->urlBuilder = $urlBuilder;
        parent::__construct($context, $uiComponentFactory, $components, $data);
    }

    public function prepareDataSource(array $dataSource)
    {
        if (isset($dataSource['data']['items'])) {
            foreach ($dataSource['data']['items'] as & $item) {
                $item[$this->getData('name')] = [
                    'edit' => [
                        'href' => $this->urlBuilder->getUrl(
                            'landingpage_form/index/edit',
                            ['id' => $item['entity_id']]
                        ),
                        'label' => __('Edit')
                    ],
                    'delete' => [
                        'href' => $this->urlBuilder->getUrl(
                            'landingpage_form/index/delete',
                            ['id' => $item['entity_id']]
                        ),
                        'label' => __('Delete'),
                        'confirm' => [
                            'title' => __('Delete "${ $.$data.name }"'),
                            'message' => __('Are you sure you want to delete the form "${ $.$data.name }"?')
                        ]
                    ]
                ];
            }
        }
        return $dataSource;
    }
}
```

This class defines the actions (edit and delete) for each row in our grid.

### Step 11: Create the Collection Class
In `app/code/LandingPage/Form/Model/ResourceModel/Form/Collection.php`:

```php
<?php
namespace LandingPage\Form\Model\ResourceModel\Form;

use Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection;

class Collection extends AbstractCollection
{
    protected function _construct()
    {
        $this->_init('LandingPage\Form\Model\Form', 'LandingPage\Form\Model\ResourceModel\Form');
    }
}
```

This class defines the collection for our form data.

### Step 12: Update the `di.xml` File
In `app/code/LandingPage/Form/etc/di.xml`:

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <virtualType name="LandingPageFormGridDataProvider" type="Magento\Framework\View\Element\UiComponent\DataProvider\DataProvider">
        <arguments>
            <argument name="collection" xsi:type="object" shared="false">LandingPage\Form\Model\ResourceModel\Form\Collection</argument>
            <argument name="filterPool" xsi:type="object" shared="false">LandingPageFormGridFilterPool</argument>
        </arguments>
    </virtualType>
    <virtualType name="LandingPageFormGridFilterPool" type="Magento\Framework\View\Element\UiComponent\DataProvider\FilterPool">
        <arguments>
            <argument name="appliers" xsi:type="array">
                <item name="regular" xsi:type="object">Magento\Framework\View\Element\UiComponent\DataProvider\RegularFilter</item>
                <item name="fulltext" xsi:type="object">Magento\Framework\View\Element\UiComponent\DataProvider\FulltextFilter</item>
            </argument>
        </arguments>
    </virtualType>
    <type name="Magento\Framework\View\Element\UiComponent\DataProvider\CollectionFactory">
        <arguments>
            <argument name="collections" xsi:type="array">
                <item name="landingpage_form_listing_data_source" xsi:type="string">LandingPage\Form\Model\ResourceModel\Form\Grid\Collection</item>
            </argument>
        </arguments>
    </type>
    <virtualType name="LandingPage\Form\Model\ResourceModel\Form\Grid\Collection" type="Magento\Framework\View\Element\UiComponent\DataProvider\SearchResult">
        <arguments>
            <argument name="mainTable" xsi:type="string">landingpage_form</argument>
            <argument name="resourceModel" xsi:type="string">LandingPage\Form\Model\ResourceModel\Form</argument>
        </arguments>
    </virtualType>
</config>
```

This configuration sets up the data provider and collection for our grid.

### Step 13: Create the Form Model
In `app/code/LandingPage/Form/Model/Form.php`:

```php
<?php
namespace LandingPage\Form\Model;

use Magento\Framework\Model\AbstractModel;

class Form extends AbstractModel
{
    protected function _construct()
    {
        $this->_init('LandingPage\Form\Model\ResourceModel\Form');
    }
}
```

### Step 14: Create the Form Resource Model
In `app/code/LandingPage/Form/Model/ResourceModel/Form.php`:

```php
<?php
namespace LandingPage\Form\Model\ResourceModel;

use Magento\Framework\Model\ResourceModel\Db\AbstractDb;

class Form extends AbstractDb
{
    protected function _construct()
    {
        $this->_init('landingpage_form', 'entity_id');
    }
}
```

### Step 15: Create the Database Schema
Create a file `app/code/LandingPage/Form/etc/db_schema.xml`:

```xml
<?xml version="1.0"?>
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="landingpage_form" resource="default" engine="innodb" comment="Landing Page Form Table">
        <column xsi:type="int" name="entity_id" padding="10" unsigned="true" nullable="false" identity="true" comment="Entity ID"/>
        <column xsi:type="varchar" name="name" nullable="false" length="255" comment="Name"/>
        <column xsi:type="varchar" name="email" nullable="false" length="255" comment="Email"/>
        <column xsi:type="timestamp" name="created_at" on_update="false" nullable="false" default="CURRENT_TIMESTAMP" comment="Creation Time"/>
        <column xsi:type="timestamp" name="updated_at" on_update="true" nullable="false" default="CURRENT_TIMESTAMP" comment="Update Time"/>
        <constraint xsi:type="primary" referenceId="PRIMARY">
            <column name="entity_id"/>
        </constraint>
    </table>
</schema>
```

### Step 16: Create the Form Submission Page
To allow users to submit forms on the frontend, create a new controller, block, and template.

**Controller**: In `app/code/LandingPage/Form/Controller/Index/Submit.php`:

```php
<?php
namespace LandingPage\Form\Controller\Index;

use Magento\Framework\App\Action\Action;
use Magento\Framework\App\Action\Context;
use LandingPage\Form\Model\FormFactory;

class Submit extends Action
{
    protected $formFactory;

    public function __construct(Context $context, FormFactory $formFactory)
    {
        $this->formFactory = $formFactory;
        parent::__construct($context);
    }

    public function execute()
    {
        $post = $this->getRequest()->getPostValue();
        if ($post) {
            $form = $this->formFactory->create();
            $form->setData($post)->save();
            $this->messageManager->addSuccessMessage(__('Form submitted successfully.'));
        }
        return $this->_redirect('*/*/');
    }
}
```

Create a Frontend Route
In `app/code/LandingPage/Form/etc/frontend/routes.xml`:

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:App/etc/routes.xsd">
    <router id="standard">
        <route id="landingpage_form" frontName="landingpage_form">
            <module name="LandingPage_Form" />
        </route>
    </router>
</config>
```

Create a Template for the Form  
In `app/code/LandingPage/Form/view/frontend/templates/form.phtml`:

```xml
<form action="<?= $block->getFormAction() ?>" method="post">
    <fieldset class="fieldset">
        <legend class="legend"><span><?= __('Submit Form') ?></span></legend>
        <div class="field name required">
            <label class="label" for="name"><span><?= __('Name') ?></span></label>
            <div class="control">
                <input name="name" id="name" title="<?= __('Name') ?>" class="input-text" type="text" data-validate="{required:true}"/>
            </div>
        </div>
        <div class="field email required">
            <label class="label" for="email"><span><?= __('Email') ?></span></label>
            <div class="control">
                <input name="email" id="email" title="<?= __('Email') ?>" class="input-text" type="email" data-validate="{required:true, 'validate-email':true}"/>
            </div>
        </div>
    </fieldset>
    <div class="actions-toolbar">
        <div class="primary">
            <button type="submit" class="action submit primary" title="<?= __('Submit') ?>">
                <span><?= __('Submit') ?></span>
            </button>
        </div>
    </div>
</form>
```

### Step 17: Create a Layout File for the Frontend Form  
Create `app/code/LandingPage/Form/view/frontend/layout/landingpage_form_index_index.xml`:

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" layout="1column" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="content">
            <block class="Magento\Framework\View\Element\Template" name="landingpage_form" template="LandingPage_Form::form.phtml"/>
        </referenceContainer>
    </body>
</page>
```

That completes the tutorial for building a custom admin grid in Magento 2 with a frontend submission form. Here's a summary of what we've accomplished:

- Created the module structure
- Set up the admin menu and routes
- Created the admin grid UI component
- Implemented the data provider and collection
- Created the Form model and resource model
- Set up the database schema
- Implemented a frontend form for submissions

### To Use This Module:
1. Place the code in `app/code/LandingPage/Form/`
2. Run `bin/magento setup:upgrade`
3. Run `bin/magento setup:di:compile`
4. Run `bin/magento cache:clean`

You should now see a "Landing Pages" menu item in the admin panel, and you can access the frontend form at `yourstore.com/landingpage_form`.

---

Is there anything specific you'd like me to explain further or any modifications you'd like to make to this module?
