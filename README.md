### EX8 Web Scraping On E-commerce platform using BeautifulSoup
### DATE: 25.08.2026
### AIM: To perform Web Scraping on Amazon using (beautifulsoup) Python.
### Description: 
<div align = "justify">
Web scraping is the process of extracting data from various websites and parsing it. In other words, it’s a technique 
to extract unstructured data and store that data either in a local file or in a database. 
There are many ways to collect data that involve a huge amount of hard work and consume a lot of time. Web scraping can save programmers many hours. Beautiful Soup is a Python web scraping library that allows us to parse and scrape HTML and XML pages. 
One can search, navigate, and modify data using a parser. It’s versatile and saves a lot of time.
<p>The basic steps involved in web scraping are:
<p>1) Loading the document (HTML content)
<p>2) Parsing the document
<p>3) Extraction
<p>4) Transformation

### Procedure:

1) Import necessary libraries (requests, BeautifulSoup, re, matplotlib.pyplot).
2) Define convert_price_to_float(price) Function: to Remove non-numeric characters from a price string and convert it to a float.
3) Define get_amazon_products(search_query) Function: to Scrape Amazon for product information based on the search query.
4) Fetch and parse the HTML content then Extract product names and prices from the search results and Sort product information based on converted prices in ascending order.
5) Return sorted product data as a list of dictionaries.
6) Call get_amazon_products(search_query) to get product data based on the user's search query.
7) Check if products are found; if not, display "No products found."
8) Visualize Product Data using a Bar Chart

### Program:
```PYTHON
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.common.exceptions import TimeoutException
import time
import matplotlib.pyplot as plt
import re

# --------------------------------
# INPUT
# --------------------------------

query = input("Enter the query to search: ").lower().strip()
pages = int(input("Enter number of pages to traverse: "))

found = []


# --------------------------------
# CHROME SETUP
# --------------------------------

options = Options()
options.add_argument("--start-maximized")
options.page_load_strategy = "eager"

driver = webdriver.Chrome(options=options)

driver.set_page_load_timeout(30)


# --------------------------------
# SEARCH AMAZON
# --------------------------------

for page in range(1, pages + 1):

    url = (
        "https://www.amazon.in/s?k="
        + query.replace(" ", "+")
        + "&page="
        + str(page)
    )

    print(f"\nChecking Page {page}...")

    try:
        driver.get(url)

    except TimeoutException:
        print("Page loading timed out. Continuing...")

    time.sleep(5)

    # Find Amazon product containers
    products = driver.find_elements(
        By.CSS_SELECTOR,
        'div[data-component-type="s-search-result"]'
    )

    print("Products found on page:", len(products))


    # --------------------------------
    # EXTRACT PRODUCT INFORMATION
    # --------------------------------

    for product in products:

        try:

            # Product title
            title = product.find_element(
                By.CSS_SELECTOR,
                "h2 span"
            ).text.strip().lower()


            # ----------------------------
            # PRODUCT PRICE
            # ----------------------------

            try:

                whole = product.find_element(
                    By.CSS_SELECTOR,
                    ".a-price-whole"
                ).get_attribute("textContent")

                whole = whole.strip().replace(",", "")

                try:
                    fraction = product.find_element(
                        By.CSS_SELECTOR,
                        ".a-price-fraction"
                    ).get_attribute("textContent")

                    fraction = fraction.strip()

                except:
                    fraction = "00"

                price = "₹" + whole + "." + fraction

            except:

                price = "Price not available"


            # ----------------------------
            # RATING
            # ----------------------------

            try:

                rating = product.find_element(
                    By.CSS_SELECTOR,
                    "span.a-icon-alt"
                ).get_attribute("textContent")

            except:

                rating = "N/A"


            # ----------------------------
            # REVIEWS
            # ----------------------------

            try:

                review = product.find_element(
                    By.CSS_SELECTOR,
                    "span.a-size-base.s-underline-text"
                ).text.strip()

            except:

                review = "N/A"


            # Store product
            found.append({
                "title": title,
                "price": price,
                "rating": rating,
                "review": review
            })


        except Exception:
            continue


    time.sleep(2)


# --------------------------------
# CLOSE AMAZON
# --------------------------------

driver.quit()


# --------------------------------
# REMOVE DUPLICATES
# --------------------------------

unique_products = []
seen = set()

for item in found:

    if item["title"] not in seen:

        seen.add(item["title"])
        unique_products.append(item)

found = unique_products


# --------------------------------
# DISPLAY RESULTS
# --------------------------------

print("\nResults Found:", len(found))
print()


for j, item in enumerate(found[:10], 1):

    print("Product:", j)
    print("Title :", item["title"])
    print("Price :", item["price"])
    print("Rating :", item["rating"])
    print("Review :", item["review"])
    print()


# --------------------------------
# PREPARE GRAPH DATA
# --------------------------------

if found:

    product_names = []
    product_prices = []

    for item in found[:10]:

        price_text = item["price"]

        # Remove ₹ and commas
        price_text = price_text.replace("₹", "")
        price_text = price_text.replace(",", "")
        price_text = price_text.strip()

        try:

            price = float(price_text)

            product_names.append(
                item["title"][:25]
            )

            product_prices.append(price)

        except:

            continue


    # --------------------------------
    # CHECK PRICE DATA
    # --------------------------------

    print("Prices used for graph:")
    print(product_prices)


    if product_prices:

        # Sort products by price
        data = sorted(
            zip(product_names, product_prices),
            key=lambda x: x[1]
        )

        product_names = [
            x[0] for x in data
        ]

        product_prices = [
            x[1] for x in data
        ]


        # --------------------------------
        # CREATE GRAPH
        # --------------------------------

        plt.figure(figsize=(12, 8))

        plt.barh(
            product_names,
            product_prices
        )

        plt.xlabel("Price (₹)")
        plt.ylabel("Product Name")

        plt.title(
            "Mobile Prices from Amazon Web Scraping"
        )

        plt.tight_layout()

        plt.show()


    else:

        print("No valid prices available for graph.")


else:

    print("No products found to plot.")

```

### Output:

<img width="1255" height="894" alt="Screenshot 2026-08-25 112402" src="https://github.com/user-attachments/assets/929c5395-9fb9-4acc-a301-4c28f09d3ce0" />

<img width="1249" height="889" alt="Screenshot 2026-08-25 112418" src="https://github.com/user-attachments/assets/41113cf3-672b-481c-aee9-13591dd536b6" />

### Result:

Thus, We had Successfully implemented the Web Scraping on a E-commerce platform using (beautifulsoup) Python.
