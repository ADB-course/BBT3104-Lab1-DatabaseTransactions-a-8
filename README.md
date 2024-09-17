[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/r-tQZu0l)
# BBT3104-Lab1of6-DatabaseTransactions


| **Key**                                                               | Value                                                                                                                                                                              |
|---------------|---------------------------------------------------------|
| **Group 
<!-- A8
Name**                                                               | 
Branice Nanzala
Bridgette Juma
Gerald Raila
Tedd Muunda
Veronicah mbuvi | -->
| **Semester Duration**                                                 | 19<sup>th</sup> August - 25<sup>th</sup> November 2024                                                                                                                       |

## Flowchart
// TRANSACTION PROCESS
START
->Create MySQL docker container
->create a connection to the container using DBeaver
->Turn off autocommit
->set Isolation
->Start Transaction
->Check Maximum Order Number
->Insert New Order
->Save the Transaction Before Adding Products
->Add Product 1 to the Order
->Save the Progress Again
->Add Product 2 to the Order
->Rollback if Something Goes Wrong
->Add Product 3 to the Order
->Record the Payment
->Commit the Transaction
->END
## Pseudocode
// Start Transaction Process
START TRANSACTION;

// Select the database
USE classicmodels; 

// Step 1: Calculate the next order number
SET @orderNumber = (SELECT MAX(orderNumber) + 1 FROM orders);

// Step 2: Insert a new order
INSERT INTO orders(orderNumber, orderDate, requiredDate, shippedDate, status, customerNumber)
VALUES (@orderNumber, DATE(NOW()), DATE_ADD(NOW(), INTERVAL 3 DAY), DATE_ADD(NOW(), INTERVAL 2 DAY), 'In Process', 145);

// Step 3: Savepoint before first product is inserted
SAVEPOINT before_product_1;

// Step 4: Insert first product and update stock
INSERT INTO orderdetails(orderNumber, productCode, quantityOrdered, priceEach, orderLineNumber)
VALUES (@orderNumber, 'S18_1749', 2724, 136, 1);

SET @quantityInStock = (SELECT quantityInStock FROM products WHERE productCode = 'S18_1749');

UPDATE products SET quantityInStock = @quantityInStock - 2724 WHERE productCode = 'S18_1749';

// Step 5: Savepoint before second product is inserted
SAVEPOINT before_product_2;

// Step 6: Insert second product and update stock
INSERT INTO orderdetails(orderNumber, productCode, quantityOrdered, priceEach, orderLineNumber)
VALUES (@orderNumber, 'S18_2248', 540, 55.09, 2);

SET @quantityInStock = (SELECT quantityInStock FROM products WHERE productCode = 'S18_2248');

UPDATE products SET quantityInStock = @quantityInStock - 540 WHERE productCode = 'S18_2248';

// Step 7: Rollback to the savepoint before second product in case of error
ROLLBACK TO SAVEPOINT before_product_2;

// Step 8: Savepoint before third product is inserted
SAVEPOINT before_product_3;

// Step 9: Insert the second product ordered and update  stock
INSERT INTO orderdetails(orderNumber, productCode, quantityOrdered, priceEach, orderLineNumber)
VALUES (@orderNumber, 'S12_1099', 68, 95.34, 3);

SET @quantityInStock = (SELECT quantityInStock FROM products WHERE productCode = 'S12_1099');

UPDATE products SET quantityInStock = @quantityInStock - 68 WHERE productCode = 'S12_1099';

// Step 10: Receive and Record the payment
INSERT INTO payments(customerNumber, checkNumber, paymentDate, amount)
VALUES (145, 'JM555210', DATE(NOW()), 300000);

// Step 11: Commit the transaction
COMMIT;

## Support for the Sales Departments' Report

totalOrderAmount(The total amount due for the order).
amountPaid(The total amount paid so far for the order).
balanceDue(The amount remaining to be paid).
paymentStatus(Indicator of whether the order has been fully paid).