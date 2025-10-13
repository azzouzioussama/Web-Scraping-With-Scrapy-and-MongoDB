# Web Scraping With Scrapy and MongoDB

A Python project for web scraping data using the Scrapy framework and storing the extracted information in MongoDB.

## Overview

This project demonstrates how to build efficient web scrapers with Scrapy and persist the data in MongoDB for further analysis or usage.

## Features

- Customizable web scrapers built with Scrapy
- MongoDB integration for data storage
- Configurable crawling settings
- Data processing pipelines
- Export options (JSON, CSV)

## Technologies Used

- Python 3.x
- Scrapy
- MongoDB
- pymongo

## Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/yourusername/web-scraping-scrapy-mongodb.git
    cd web-scraping-scrapy-mongodb
    ```

2. Create and activate a virtual environment (optional but recommended):
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```

3. Install dependencies:
    ```bash
    pip install -r requirements.txt
    ```

4. Install and start MongoDB (if not already installed):
    - [MongoDB Installation Guide](https://docs.mongodb.com/manual/installation/)

## Usage

1. Configure your MongoDB connection in `settings.py`:
    ```python
    MONGO_URI = 'mongodb://localhost:27017'
    MONGO_DATABASE = 'scrapy_data'
    ```

2. Run a spider:
    ```bash
    scrapy crawl example_spider
    ```

3. Check MongoDB for collected data:
    ```python
    from pymongo import MongoClient
    client = MongoClient('mongodb://localhost:27017')
    db = client.scrapy_data
    data = db.items.find()
    for item in data:
         print(item)
    ```

## Project Structure

```
.
├── scrapy.cfg
├── requirements.txt
├── README.md
└── project/
     ├── __init__.py
     ├── items.py
     ├── middlewares.py
     ├── pipelines.py
     ├── settings.py
     └── spiders/
          ├── __init__.py
          └── example_spider.py
```

## Creating a New Spider

```python
# project/spiders/example_spider.py
import scrapy
from project.items import ProjectItem

class ExampleSpider(scrapy.Spider):
     name = 'example_spider'
     start_urls = ['https://example.com']
     
     def parse(self, response):
          item = ProjectItem()
          item['title'] = response.css('h1::text').get()
          item['content'] = response.css('p::text').get()
          yield item
```

## MongoDB Pipeline

The project includes a MongoDB pipeline to store scraped items:

```python
# project/pipelines.py
import pymongo

class MongoPipeline:
     def __init__(self, mongo_uri, mongo_db):
          self.mongo_uri = mongo_uri
          self.mongo_db = mongo_db

     @classmethod
     def from_crawler(cls, crawler):
          return cls(
                mongo_uri=crawler.settings.get('MONGO_URI'),
                mongo_db=crawler.settings.get('MONGO_DATABASE')
          )

     def open_spider(self, spider):
          self.client = pymongo.MongoClient(self.mongo_uri)
          self.db = self.client[self.mongo_db]

     def close_spider(self, spider):
          self.client.close()

     def process_item(self, item, spider):
          self.db[spider.name].insert_one(dict(item))
          return item
```

## License

MIT License

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request


# test:
https://drive.google.com/drive/folders/1RLprT0dZp4oQ4lG1KpnijtMKcas9vlB3?usp=sharing