
# Google Dorking


### Search operators

```bash
"django developer"               # "" for extact match
python -django              # - for exclude (no space) 
"admin * panel"             # * for Wildcard (any word)
react and django            # AND
react or django             # OR
react | django                  # OR
(react and nodejs) | (react and djnago)        # paranteses for grouping
```


### Site and Domain Search

```bash
site:example.com react	           # Search only that domain
inurl:admin	                  # Term must be in URL
allinurl:admin login           # All terms in URL
intitle:"index of"	             # Term in page title
allintitle:login panel	             # All terms in title
```


###  File Types and Directories

```bash
filetype:pdf cheatsheet	          # Search specific file types
ext:sql	customers                         # Alternative to filetype
"index of /backup"	             #  'index of' for opening directories listing files
```
























