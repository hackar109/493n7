import requests
from bs4 import BeautifulSoup
import time

def google_search(query):
    url = f"https://www.google.com/search?q={query}"
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
    }

    try:
        response = requests.get(url, headers=headers)
        response.raise_for_status()
        soup = BeautifulSoup(response.text, "html.parser")
        
        # Parse search results
        links = []
        for item in soup.select("a"):
            href = item.get("href")
            if href and "/url?q=" in href:
                link = href.split("/url?q=")[1].split("&sa=U")[0]
                links.append(link)
        
        return links
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")
        return []

def google_dork(target_domain, dork_queries):
    for dork in dork_queries:
        query = f"site:{target_domain} {dork}"
        print(f"\n[+] Running dork: {query}")
        results = google_search(query)
        for link in results:
            print(f"Found URL: {link}")
        time.sleep(2)  # Respectful delay between requests

if __name__ == "__main__":
    target_domain = input("Enter target domain (e.g., example.com): ")
    dork_queries = [
        "inurl:admin",            # Admin pages
        "filetype:sql password",  # Database files
        "intitle:index.of",       # Directory listings
        "filetype:log",           # Log files
        "inurl:wp-content/uploads" # WordPress uploads
    ]
    google_dork(target_domain, dork_queries)
