
# BeautifulSoap


## install 

```bash
pip install beautifulsoup4 
pip install lxml
```

## general usage

```py
from bs4 import BeautifulSoup
html = "<html><body><h1>Hello</h1><p class='msg'>World</p></body></html>"
soup = BeautifulSoup(html, "lxml")          # pip install lxml
soup = BeautifulSoup(html, "html.parser")   

# Parsing
soup.title           # <title>...</title>
soup.title.text      # text inside
soup.h1              # first <h1>
soup.find("p")       # first <p>
soup.find_all("p")   # list of all <p>

# By tag
soup.find("div")
soup.find_all("a")

# By class
soup.find("p", class_="msg")
soup.select(".msg")   # CSS selector

# By id
soup.find(id="main")
soup.select("#main")

# By attributes
soup.find("a", {"href": True})
soup.find_all("img", {"src": lambda v: v.endswith(".png")})

```
