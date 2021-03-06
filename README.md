# PRINTIFY PRODUCT API

Recreated "printify-api-lauris" API with the PHP Symfony framework.
Project runs on nginx server, php scripts are ran by php-fpm and data is stored in mysql. Phpmyadmin is enabled too.

This is an API which allows to add and view products.

It is also possible to create order consisting of products and view specific order, orders and filter orders by a specific type of product within.

## PROJECT FOLDER STRUCTURE

The main source files of the API are stored in **src** directory.

```
src
├── Controller [source files for endpoints]
	├── OrdersController.php [handles API endpoints related to orders]
	├── ProductsController.php [handles API endpoints related to products]
├── Entity [Structure of rows (objects) within each table]
	├── CountryCodes.php [describes fields of country code object tracking requests count]
	├── Order.php [describes fields of order object]
	├── Product.php [describes fields of product object]
	├── ProductOrderRelation.php [describes fields of each object describing order and it's products]
├── InvoiceFiles [Has a class for creating an object to generate HTML of an invoice for order]
├── Migrations [Default folder for Doctrine. Migratins have not been used thus far.]
├── OrderFiles [source files for each endpoint]
	├── OrderDataValidator.php [Class to validate order data]
	├── OrderErrorInfo.php [Class for turning error code into a message]
	├── OrderRepository.php [functions for working with 'product_order_relation' database]
├── ProductFiles [source files for each endpoint]
	├── ProductDataStandardise.php [Class to standardise product data from posted JSON data to meet database standard]
	├── ProductDataValidator.php [Class to validate product data]
	├── ProductErrorInfo.php [Class for turning error code into a message]
	├── ProductFetcher.php [class to fetch a product given characteristics]
├── Repository [files for each Entity to access database]
DataGetter.php [Class to get posted JSON data]
Kernel.php [Core function of Symfony to set the project up. No changes made]
KernelRequest.php [Custom made class acting as a middleware set request limit]
```

## INSTALLATION

In root directory run

```
composer install
docker-compose up
```

Log into phpmyadmin **http://localhost:8088** using:

```
username: root
password: rootroot
```

Then, navigate to **printify_api database** and head to **Events** section. Finally, press **Event scheduler status** button. This sets up properly request filter.

## DATABASE INSTALLATION

This project uses mysql database and doctrine within the symfony project to edit it.
After executing **docker-compose up** mysql is restored from **mysql-dump/printify_api.sql** file. Four tables - country_codes, order, product and product_order_relations - are automatically created empty.

**country_codes** - keep track of requests from each country


**product** - stores submitted products


**order** - stores submitted orders


**product_order_relation** - links an order with it's products


Connection to **mysql** docker container is defined in **config/services.yaml DATABASE_URL** field

## USAGE

Endpoints can be accessed at port 8098:

http://localhost:8098/{endpoint}

**phpMyAdmin** can be accessed at **http://localhost:8088** using:

```
username: root
password: rootroot
```


## ENDPOINTS

### products POST

POST request

Add a product

http://localhost:8098/products
```
{
	"price" : "10.00",
	"type" : "t-shirt",
	"color" : "blue",
	"size" : "S"
}
```
"price" field needs to be a string


"type" field can be "t-shirt", "socks", "hoodie", "beanie", "slippers", "jacket".

"color" field can be "black", "white", "blue", "red", "yellow", "purple".

"size" field can be "XS", "S", "M", "L", "XL", "XXL" or number in between a string e.g "40".

### products GET

GET request

View all products

http://localhost:8098/products


View one product by it's id

http://localhost:8098/products/{id}

All values returned except **id** of the product are strings.

### orders POST

POST request

Add an order

http://localhost:8098/orders

```

{
	"name" : "john doe",
	"street" : "pineapple street 7-89",
	"city" : "san diego",
	"country" : "USA",
	"postalcode" : "22434",
	"1" (product ID) : "2" (quantity)
}
```

Must include address fields and then each item is added to the order as it's ID and quantity.
After that and HTML response is returned as an invoice for the order. If you are using Postman for testing,
browse to **Preview** tab.

### orders GET

GET request

Viewing an order

http://localhost:8098/orders/{id}

Viewing orders

http://localhost:8098/orders

Viewing orders that contain item of a specific type of product

http://localhost:8098/orders?type={type}

All values returned except **orderId** and **productId** are strings.

## RESTRICTING CONNECTIONS

By default, it is set that in 1 minute 5 requests from country are allowed if this option is enabled.

To change that, go to **config/services.yaml** and under **App\KernelRequest** change **$requestsPerMinuteLimit** to your desired limit.

