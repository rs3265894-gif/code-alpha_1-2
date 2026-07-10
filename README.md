# CODEALPHA - TASK 1: Web Scraping
import requests
from bs4 import BeautifulSoup
import pandas as pd
import time

def scrape_books():
    base_url = "http://books.toscrape.com/catalogue/"
    all_books = []
    rating_map = {"One":1,"Two":2,"Three":3,"Four":4,"Five":5}

    print("Scraping books.toscrape.com...")

    for page_num in range(1, 6):
        url = f"http://books.toscrape.com/catalogue/page-{page_num}.html"
        print(f"Page {page_num} scraping...")

        response = requests.get(url, timeout=10)
        soup = BeautifulSoup(response.text, "html.parser")
        books = soup.find_all("article", class_="product_pod")

        for book in books:
            title = book.h3.a["title"]
            price = float(book.find("p", class_="price_color").text.strip().replace("£","").replace("Â",""))
            rating = rating_map.get(book.find("p", class_="star-rating")["class"][1], 0)
            availability = book.find("p", class_="instock availability").text.strip()

            all_books.append({
                "Title": title,
                "Price_GBP": price,
                "Rating": rating,
                "Availability": availability
            })
        time.sleep(0.5)

    df = pd.DataFrame(all_books)
    df.to_csv("books_dataset.csv", index=False)
    print(f"\n✅ Done! {len(df)} books scraped.")
    print(df.head())
    return df
if __name__ == "__main__":
    scrape_books()
