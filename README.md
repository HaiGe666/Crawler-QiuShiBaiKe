# Crawler-QiuShiBaiKe
练手的糗事百科爬虫

import requests
from bs4 import BeautifulSoup
import re
import json

url = 'https://www.qiushibaike.com/text/page/{}'


detail = {}
res = requests.get(url)
res.encoding = 'utf-8'
soup = BeautifulSoup(res.text, 'html.parser')

def getId(url):
    id = []
    for i in range(1,50,2):
        id.append(soup.select('#content-left')[0].contents[i]['id'].lstrip('qiushi_tag_'))
    return id

aUrl = 'https://www.qiushibaike.com/article/{}'
def getDetails(url):
    detail = {}
    res = requests.get(url)
    res.encoding = 'utf-8'
    soup = BeautifulSoup(res.text, 'html.parser')
    detail['content'] = soup.select('.content')[0].text.strip().replace('', ',')
    detail['author'] = soup.select('.author')[0].text.strip().split('\n')[0].replace('', ',')
    detail['stats'] = soup.select('.stats')[0].contents[1].text.rstrip(' 好笑')
    if soup.select('.main-text') == []:
        detail['main-text'] = '暂时没有神评论'
    else:
        detail['main-text'] = soup.select('.main-text')[0].text.replace('', ',')
    return detail

total = []
num = int(input('输入要爬取的页数：'))
for i in range(1,(num+1)):
    pageUrl = url.format(i)
    id = getId(pageUrl)
    for j in id:
        articleUrl = aUrl.format(j)
        temp = getDetails(articleUrl)
        total.append(temp)
    print('第%d页爬取成功' % i)
    print('-----------------------------------------------')

import pandas
df = pandas.DataFrame(total)

df.to_excel('fun.xlsx')
