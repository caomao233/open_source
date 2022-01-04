import requests as req
from bs4 import BeautifulSoup as bs
import lxml
import pandas as pd

class House_Spider():
    def __init__(self):
        self.url = 'https://sh.ke.com/ershoufang/pg'
        self.head={
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.106 Safari/537.36'
            }
        self.proxies = {
            'http://':'210.12.14.15:80',
            'https://':'210.12.14.15:80'
        }
        self.response = ''
        self.url_list = []
        self.data_list = []
        self.houses = {}

    def MainPage(self):
        self.response = req.get(url = self.url, headers = self.head, proxies = self.proxies)
        print("爬完一面啦")
        soup = bs(self.response.text, "html.parser")
        title = soup.title
        print(title)
        home_list = soup.find(name="ul",attrs={"class":"sellListContent"}).find_all(name="li",attrs={"class":"clear"})
        for i in range(len(home_list)):
            self.url_list.append(home_list[i].a.attrs.get('href'))

    def SecondPage(self, x):
        url = self.url_list[x]
        self.response = req.get(url=url, headers=self.head, proxies=self.proxies)
        html = lxml.etree.HTML(self.response.text)
        print("+1")
        data_list = []
        # 地址
        home_location = html.xpath('//div[@data-component="overviewIntro"]//div[@class="content"]//div[@class="areaName"]/span[@class="info"]/a/text()')
        data_list.append(home_location)
        # 小区
        local_name = html.xpath('//div[@data-component="overviewIntro"]//div[@class="content"]//div[@class="communityName"]/a/text()')
        data_list.append(local_name)
        # 总价格
        total_price = html.xpath('//div[@data-component="overviewIntro"]//div[@class="content"]//div[@class="price "]/span[@class="total"]/text()')[0]
        data_list.append(total_price)
        # 单价
        unit_price = html.xpath('//div[@data-component="overviewIntro"]//div[@class="content"]//div[@class="price "]//div[@class="unitPrice"]/span/text()')[0]
        data_list.append(unit_price)
        # 房屋基本信息
        home_style = html.xpath('//div[@class="introContent"]//div[@class="base"]//div[@class="content"]/ul/li/text()')
        data_list.append(home_style)
        self.data_list.append(data_list)

    def save_data(self, data):
        data_frame = pd.DataFrame(data, columns=['小区位置','小区名称','房屋总价','房屋单价','房屋基本信息'])
        data_frame.to_csv('ShangHaiHouse_Price.csv', header=False, index=False, mode='a', encoding='utf_8_sig')
        print(data_frame)
    
    def save_mongodb(self,datas):
        
        for i in range(0,len(datas)):
            print(datas[i])
            self.houses[i]={
                'home_location':str(datas[i][0]),
                'local_name':str(datas[i][1]),
                'total_price':str(datas[i][2]),
                'unit_price':str(datas[i][3]),
                'home_style':str(datas[i][4])
            }
            result=self._houses.insert_one(self.houses[i])
            print(result.acknowledged)

if __name__ == "__main__":
    House = House_Spider()
    for i in range(0, 10):
        House.url = 'https://sh.ke.com/ershoufang/pg' + str(i) + '/'
        House.MainPage()
    print(len(House.url_list))

    for i in range(len(House.url_list)):
        House.SecondPage(i)

    House.save_data(House.data_list)
