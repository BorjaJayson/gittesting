import requests
from lxml.html import fromstring
import pprint 
import re

url = "https://jobs.mo.gov/content/missouri-warn-notices-py-2017"
response = requests.get(url)
root = fromstring(response.content)

table = root.xpath('.//*[@summary="Missouri WARN Notices PY 2016"]')[0] 
tableRes = []
columnHeaders = table.xpath(".//tr//th/span/text()")

for row in table.xpath(".//tr")[1:]:
    i = 0
    rowDict = {}
    for col in row.xpath(".//td"):
        if i != 1:
            rowDict[columnHeaders[i]] = re.sub(r"[\n\t]*", "","".join(col.xpath(".//text()")).replace(u'\xa0', u' '))
        else:
            rowDict[columnHeaders[i]] = re.sub(r"[\n\t]*", "","".join(col.xpath(".//a/text()")).replace(u'\xa0', u' '))
        i += 1
    tableRes.append(rowDict)

pp = pprint.PrettyPrinter(indent=4)
pp.pprint(tableRes)
