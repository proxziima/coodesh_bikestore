# Bike Stores - Teste técnico de Análise de Dados

## Objetivo

Efetuar uma análise de dados da empresa Bike Stores para obter as métricas relevantes para as consultas solicicitadas no teste técnico.

This is a challenge by [Coodesh](https://coodesh.com/)

## Tecnologias usadas

1. **SQL**: para a criação da base de dados e consulta dos dados
2. **Python e Jupyter Notebook**: para a criação do processo de análise e notação das consultas.

## Passo a Passo

### Instalação

Instalação dos módulos

```
pip install pysqlite3 pandas

```
Importação dos módulos

```
import pandas as pd
import sqlite3

```

Criando as tabelas e os seus relacionamentos na coluna.

Para criar as tabelas utilizei SQL através da função 'CREATE TABLE' para tais ações:

CREATE TABLE IF NOT EXISTS customers (
        customer_id INTEGER PRIMARY KEY,
        first_name TEXT,
        last_name TEXT,
        phone TEXT,
        email TEXT,
        street TEXT,
        city TEXT,
        state TEXT,
        zip_code TEXT
    );

    CREATE TABLE IF NOT EXISTS staffs (
        staff_id INTEGER PRIMARY KEY,
        first_name TEXT,
        last_name TEXT,
        email TEXT,
        phone TEXT,
        active INTEGER,
        store_id INTEGER,
        manager_id INTEGER,
        FOREIGN KEY (store_id) REFERENCES stores(store_id),
        FOREIGN KEY (manager_id) REFERENCES staffs(staff_id)
    );

    CREATE TABLE IF NOT EXISTS stores (
        store_id INTEGER PRIMARY KEY,
        store_name TEXT,
        phone TEXT,
        email TEXT,
        street TEXT,
        city TEXT,
        state TEXT,
        zip_code TEXT
    );

    CREATE TABLE IF NOT EXISTS orders (
        order_id INTEGER PRIMARY KEY,
        customer_id INTEGER,
        order_status TEXT,
        order_date TEXT,
        required_date TEXT,
        shipped_date TEXT,
        store_id INTEGER,
        staff_id INTEGER,
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
        FOREIGN KEY (store_id) REFERENCES stores(store_id),
        FOREIGN KEY (staff_id) REFERENCES staffs(staff_id)
    );

    CREATE TABLE IF NOT EXISTS order_items (
        order_id INTEGER,
        item_id INTEGER,
        product_id INTEGER,
        quantity INTEGER,
        list_price REAL,
        discount REAL,
        PRIMARY KEY (order_id, item_id),
        FOREIGN KEY (order_id) REFERENCES orders(order_id),
        FOREIGN KEY (product_id) REFERENCES products(product_id)
    );

    CREATE TABLE IF NOT EXISTS categories (
        category_id INTEGER PRIMARY KEY,
        category_name TEXT
    );

    CREATE TABLE IF NOT EXISTS products (
        product_id INTEGER PRIMARY KEY,
        product_name TEXT,
        brand_id INTEGER,
        category_id INTEGER,
        model_year INTEGER,
        list_price REAL,
        FOREIGN KEY (brand_id) REFERENCES brands(brand_id),
        FOREIGN KEY (category_id) REFERENCES categories(category_id)
    );

    CREATE TABLE IF NOT EXISTS stocks (
        store_id INTEGER,
        product_id INTEGER,
        quantity INTEGER,
        PRIMARY KEY (store_id, product_id),
        FOREIGN KEY (store_id) REFERENCES stores(store_id),
        FOREIGN KEY (product_id) REFERENCES products(product_id)
    );

    CREATE TABLE IF NOT EXISTS brands (
        brand_id INTEGER PRIMARY KEY,
        brand_name TEXT
    );

Após esta etapa, eu inseri alguns valores de exemplos nas tabelas, permitindo que eu pudesse testar as consultas. Para utilizei as funções `INSERT INTO`com os valores de exemplo:

```
-- Inserindo registros na tabela customers
    INSERT INTO customers VALUES 
        (1, 'John', 'Doe', '1234567890', 'john@example.com', '123 Main St', 'CityA', 'StateA', '12345'),
        (2, 'Jane', 'Doe', '0987654321', 'jane@example.com', '456 Market St', 'CityB', 'StateB', '23456'),
        (3, 'Mike', 'Ross', '1111111111', 'mike@example.com', '789 Elm St', 'CityC', 'StateA', '34567'),
        (4, 'Rachel', 'Zane', '2222222222', 'rachel@example.com', '1010 Pine St', 'CityD', 'StateB', '45678'),
        (5, 'Harvey', 'Specter', '3333333333', 'harvey@example.com', '1111 Oak St', 'CityE', 'StateA', '56789'),
        (6, 'Jessica', 'Pearson', '4444444444', 'jessica@example.com', '1313 Cedar St', 'CityF', 'StateC', '67890'),
        (7, 'Louis', 'Litt', '5555555555', 'louis@example.com', '1515 Maple St', 'CityG', 'StateD', '78901'),
        (8, 'Donna', 'Paulsen', '6666666666', 'donna@example.com', '1717 Birch St', 'CityH', 'StateE', '89012'),
        (9, 'Sheila', 'Sazs', '7777777777', 'sheila@example.com', '1919 Walnut St', 'CityI', 'StateF', '90123'),
        (10, 'Jeff', 'Malone', '8888888888', 'jeff@example.com', '2121 Cherry St', 'CityJ', 'StateG', '12321');

    -- Inserindo registros na tabela stores
    INSERT INTO stores VALUES 
        (1, 'Store A', '1111111111', 'storeA@example.com', '101 Main St', 'CityA', 'StateA', '12345'),
        (2, 'Store B', '2222222222', 'storeB@example.com', '202 Market St', 'CityB', 'StateB', '23456'),
        (3, 'Store C', '3333333333', 'storeC@example.com', '303 Pine St', 'CityC', 'StateC', '34567'),
        (4, 'Store D', '4444444444', 'storeD@example.com', '404 Elm St', 'CityD', 'StateD', '45678'),
        (5, 'Store E', '5555555555', 'storeE@example.com', '505 Oak St', 'CityE', 'StateE', '56789'),
        (6, 'Store F', '6666666666', 'storeF@example.com', '606 Cedar St', 'CityF', 'StateF', '67890'),
        (7, 'Store G', '7777777777', 'storeG@example.com', '707 Birch St', 'CityG', 'StateG', '78901'),
        (8, 'Store H', '8888888888', 'storeH@example.com', '808 Walnut St', 'CityH', 'StateH', '89012'),
        (9, 'Store I', '9999999999', 'storeI@example.com', '909 Maple St', 'CityI', 'StateI', '90123'),
        (10, 'Store J', '1010101010', 'storeJ@example.com', '1010 Cherry St', 'CityJ', 'StateJ', '12321');

    -- Inserindo registros na tabela staffs
    INSERT INTO staffs VALUES 
        (1, 'John', 'Smith', 'john.smith@example.com', '9999999999', 1, 1, NULL),
        (2, 'Sarah', 'Johnson', 'sarah.johnson@example.com', '8888888888', 1, 2, 1),
        (3, 'Tom', 'Brown', 'tom.brown@example.com', '7777777777', 1, 3, 1),
        (4, 'Anna', 'Davis', 'anna.davis@example.com', '6666666666', 1, 4, 2),
        (5, 'Robert', 'Wilson', 'robert.wilson@example.com', '5555555555', 1, 5, 2);

    -- Inserindo registros na tabela categories
    INSERT INTO categories VALUES
        (1, 'Mountain Bikes'),
        (2, 'Road Bikes'),
        (3, 'Hybrid Bikes'),
        (4, 'Electric Bikes'),
        (5, 'BMX Bikes'),
        (6, 'Cruisers'),
        (7, 'Kids Bikes'),
        (8, 'Folding Bikes'),
        (9, 'Touring Bikes'),
        (10, 'City Bikes');

    -- Inserindo registros na tabela brands
    INSERT INTO brands VALUES
        (1, 'Brand A'),
        (2, 'Brand B'),
        (3, 'Brand C'),
        (4, 'Brand D'),
        (5, 'Brand E'),
        (6, 'Brand F'),
        (7, 'Brand G'),
        (8, 'Brand H'),
        (9, 'Brand I'),
        (10, 'Brand J');

    -- Inserindo registros na tabela products
    INSERT INTO products VALUES
        (1, 'Product A', 1, 1, 2023, 499.99),
        (2, 'Product B', 2, 2, 2022, 999.99),
        (3, 'Product C', 3, 3, 2023, 299.99),
        (4, 'Product D', 4, 4, 2021, 199.99),
        (5, 'Product E', 5, 5, 2020, 399.99),
        (6, 'Product F', 6, 6, 2023, 599.99),
        (7, 'Product G', 7, 7, 2022, 799.99),
        (8, 'Product H', 8, 8, 2021, 899.99),
        (9, 'Product I', 9, 9, 2020, 1099.99),
        (10, 'Product J', 10, 10, 2023, 1299.99);

    -- Inserindo registros na tabela orders
    INSERT INTO orders VALUES
        (1, 1, 'Shipped', '2024-01-01', '2024-01-05', '2024-01-03', 1, 1),
        (2, 2, 'Pending', '2024-01-02', '2024-01-06', '2024-01-04', 2, 2),
        (3, 3, 'Delivered', '2024-01-03', '2024-01-07', '2024-01-05', 3, 3),
        (4, 4, 'Shipped', '2024-01-04', '2024-01-08', '2024-01-06', 4, 4),
        (5, 5, 'Cancelled', '2024-01-05', '2024-01-09', NULL, 5, 5);

    -- Inserindo registros na tabela order_items
    INSERT INTO order_items VALUES
        (1, 1, 1, 2, 499.99, 0.10),
        (2, 1, 2, 1, 999.99, 0.05),
        (3, 1, 3, 3, 299.99, 0.15),

    -- Inserindo registros na tabela stocks
    INSERT INTO stocks VALUES
        (1, 1, 20),
        (2, 2, 30),
        (3, 3, 0),
        (4, 4, 0),
        (5, 5, 50);
```

### Consultas

Com a base criada , abaixo gerei as consultas necessárias para chegar no resultado dos Selects solicitados.

- Listar todos Clientes que não tenham realizado uma compra;

```
SELECT * FROM customers
    WHERE customer_id NOT IN (SELECT DISTINCT customer_id FROM orders);
```

- Listar os Produtos que não tenham sido comprados

```
SELECT * FROM products
    WHERE product_id NOT IN (SELECT DISTINCT product_id FROM order_items);
```

- Listar os Produtos sem Estoque;

```
SELECT p.*, COALESCE(s.quantity, 0) AS quantity
    FROM products p
    LEFT JOIN stocks s ON p.product_id = s.product_id
    WHERE COALESCE(s.quantity, 0) = 0;
```

- Agrupar a quantidade de vendas que uma determinada Marca por Loja. 

```
SELECT b.brand_name, s.store_name, COUNT(oi.product_id) AS total_sales
    FROM order_items oi
    JOIN products p ON oi.product_id = p.product_id
    JOIN brands b ON p.brand_id = b.brand_id
    JOIN orders o ON oi.order_id = o.order_id
    JOIN stores s ON o.store_id = s.store_id
    GROUP BY b.brand_name, s.store_name;
```

- Listar os Funcionarios que não estejam relacionados a um Pedido.

### Conclusão

Com base na listagem acima consegui receber os resultados esperados.

Mantive a memória de cálculo de cada etapa nos notebooks.

1. [Memória da criação das tabelas](create_database.ipynb)

2. [Memória da análise e criação das consultas](data_analysis.ipynb)
