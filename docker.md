
# Docker E-Commerce Practical

## Aim
Install Docker and deploy an E-Commerce web application consisting of at least 2 web pages.

## Step 1: Install Docker

1. Download Docker Desktop:
   https://www.docker.com/products/docker-desktop/
2. Install Docker Desktop.
3. Open Docker Desktop.
4. Wait until Docker Engine is running.

## Step 2: Check Docker Installation

Open CMD or PowerShell.

Commands:

    docker --version
    docker info
    docker ps

Expected Output:
- Docker version displayed.
- Docker Server information displayed.
- List of running containers displayed.

## Step 3: Create Project Folder

Open CMD and enter:

    mkdir ecommerce
    cd ecommerce
    code .

Note:
- mkdir ecommerce = Create folder
- cd ecommerce = Enter folder
- code . = Open folder in VS Code
_________________________________________________________________
## Step 4: Create Files in VS Code

Create these 3 files:

1. index.html

<!DOCTYPE html>
<html>
<head>
    <title>My E-Commerce Store</title>
    <style>
        body {
            font-family: Arial;
            text-align: center;
            background: #f2f2f2;
        }

        header {
            background: #222;
            color: white;
            padding: 20px;
        }

        .product {
            display: inline-block;
            background: white;
            padding: 25px;
            margin: 20px;
            border-radius: 10px;
        }

        button {
            background: green;
            color: white;
            padding: 10px;
            border: none;
            cursor: pointer;
        }
    </style>
</head>

<body>

<header>
    <h1>Welcome to My Online Store</h1>
</header>

<h2>Our Products</h2>

<div class="product">
    <h3>Laptop</h3>
    <p>Price: Rs. 50000</p>
    <button onclick="addCart('Laptop', 50000)">
        Add to Cart
    </button>
</div>

<div class="product">
    <h3>Headphones</h3>
    <p>Price: Rs. 2000</p>
    <button onclick="addCart('Headphones', 2000)">
        Add to Cart
    </button>
</div>

<p>
    <a href="cart.html">View Shopping Cart</a>
</p>

<script>
    function addCart(name, price) {
        let cart = JSON.parse(
            localStorage.getItem("cart") || "[]"
        );

        cart.push({name, price});

        localStorage.setItem(
            "cart", JSON.stringify(cart)
        );

        alert(name + " added to cart!");
    }
</script>

</body>
</html>

3. cart.html

<!DOCTYPE html>
<html>
<head>
    <title>Shopping Cart</title>
    <style>
        body {
            font-family: Arial;
            text-align: center;
            background: #f2f2f2;
        }

        header {
            background: #222;
            color: white;
            padding: 20px;
        }

        button {
            padding: 10px;
            background: red;
            color: white;
            border: none;
            cursor: pointer;
        }
    </style>
</head>

<body>

<header>
    <h1>My Shopping Cart</h1>
</header>

<div id="items"></div>

<h2 id="total"></h2>

<button onclick="clearCart()">Clear Cart</button>

<p>
    <a href="index.html">Continue Shopping</a>
</p>

<script>
    let cart = JSON.parse(
        localStorage.getItem("cart") || "[]"
    );

    let total = 0;

    cart.forEach(item => {
        document.getElementById("items").innerHTML +=
            "<p>" + item.name +
            " - Rs. " + item.price + "</p>";

        total += item.price;
    });

    document.getElementById("total").innerText =
        "Total: Rs. " + total;

    function clearCart() {
        localStorage.removeItem("cart");
        location.reload();
    }
</script>

</body>
</html>


5. Dockerfile

# Step 1: Use Nginx image
FROM nginx:alpine

# Step 2: Copy Home Page
COPY index.html /usr/share/nginx/html/

# Step 3: Copy Cart Page
COPY cart.html /usr/share/nginx/html/

# Step 4: Expose Port
EXPOSE 80

_______________________
Copy the code from the corresponding files
in this GitHub repository.

Save all files using Ctrl + S.

## Step 5: Open VS Code Terminal

Click Terminal > New Terminal.

Make sure terminal is inside ecommerce folder.

Example:

    PS C:\Users\Arya\ecommerce>

## Step 6: Build Docker Image

Command:

    docker build -t ecommerce-app .

Expected Output:

    FINISHED
    naming to docker.io/library/ecommerce-app:latest

Note:
The dot (.) at the end is important.

## Step 7: Check Docker Images

Command:

    docker images

Expected Output:

    REPOSITORY      TAG
    ecommerce-app   latest

## Step 8: Run Docker Container

Command:

    docker run -d -p 8081:80 --name ecommerce-container ecommerce-app

Expected Output:

    A long container ID

Explanation:
- docker run = Create and run container
- -d = Run in background
- -p 8081:80 = Port mapping
- --name = Container name
- ecommerce-app = Docker image name

## Step 9: Check Running Container

Command:

    docker ps

Expected Output:

    IMAGE           STATUS    PORTS
    ecommerce-app   Up        8081->80/tcp

Container Name:

    ecommerce-container

## Step 10: Open Website in Browser

Open Google Chrome.

Home Page:

    http://localhost:8081

Cart Page:

    http://localhost:8081/cart.html

Test Website:

1. Open Home Page.
2. Click Add to Cart for Laptop.
3. Click Add to Cart for Headphones.
4. Click View Shopping Cart.
5. Check total price = Rs. 52000.
6. Click Clear Cart.
7. Click Continue Shopping.

## Step 11: Additional Docker Commands

Check Docker version:

    docker --version

Check Docker information:

    docker info

List running containers:

    docker ps

List all containers:

    docker ps -a

List images:

    docker images

View container logs:

    docker logs ecommerce-container

Enter running container:

    docker exec -it ecommerce-container sh

Exit container:

    exit

Stop container:

    docker stop ecommerce-container

Start container:

    docker start ecommerce-container

Restart container:

    docker restart ecommerce-container

Remove stopped container:

    docker rm ecommerce-container

## Step 12: Perform Practical Again

If using a NEW laptop:

1. Install Docker Desktop.
2. Start Docker Engine.
3. Download or clone this repository.
4. Open the project folder in VS Code.
5. Build image.
6. Run container.
7. Open website.


