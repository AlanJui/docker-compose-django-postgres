# 專案指引

建立 Django 專案模版，以軟體開發生命週期（SDLC）為範疇，示範如何導入 docker 及 docker compose 之 VM 虛擬化技術，運用於「系統開發作業」之中。

# 系統開發作業

系統開發作業流程，由下列之「作業程序」所構成：
 - （一）建置系統開發環境作業程序
 - （二）初始資料庫作業程序
 - （三）檢視開發中系統作業程序
 
 

## （一）建置系統開發環境作業程序

為「系統開發作業」進行之流暢，需進行事前籌備工作。透過 Dockerfile 、 docker-compose.yml 兩個檔案的建置工作，用以制定及規範「開發環境（Development）」及「正式環環（Production）」。

### 1. 進入專案根目錄。

```commandline
$ cd byob-profiles-rest-api
```

### 2. 建立 Docker Build 程序設定檔。

建立檔案：Dockerfile ，並輸入以下之內容：
```buildoutcfg
ROM python:3

ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
COPY . /code/
RUN pip install -r requirements.txt
```

當執行 docker build 指令時， Docker Engine 將依據 Dockerfile 檔案內容之描述，進行 Docker Image 檔案之建置工作。

已完成建置的 Docker Image 檔案，可用 docker images 指令查詢，藉以確認建置工作的確如預期完成。

### 3. 建立 Docker Compose 程序設定檔

建立檔案：docker-compose.yml ，並輸入以下之內容：
```buildoutcfg
version: '3'

services:
  web:
    build: .
    command: python src/profiles_project/manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
```

構成「應用系統」該有的「子系統」有那些？子系統之間是否有無相依關係（譬如：啟動 Web 子系統之前， DB 子系統需先備妥）？

透過 docker-compose.yml 檔案，可對待開發的應用系統，進行此「系統結構」性的描述，而 Docker Compose 則是據此檔案的描述，啟動 Docker Container ；甚或是在 Container 啟動之前，先備妥需要使用的 Docker Image 檔案（可能是自網路下載；或是依據 Dockerfile 的描述自行 Build 建置）。

## （二）初始資料庫作業程序

令 Django Framework ，透過 Model 物件之設計，對資料庫進行初始之建置工作。

### 要求屬於 Web 的 Docker Container ，執行資料庫初始的 migrate 指令。

```commandline
docker-compose run web python src/profiles_project/manage.py migrate
```

## （三）檢視開發中系統作業程序

系統開發作業之工作，進展到某一階段，需要檢驗其功能運作；或是觀察其執行之輸出結果時，依照以下之程序步驟進行操作。

### 1. 執行應用系統啟動指令。

```commandline
$ docker-compose up
```

### 2. 啟動 Web 瀏覽器軟體，並在網址列輸入 URL 網址。

```commandline
http://localhost:8000/api
```

### 3. 在 Web 瀏覽器觀察輸出結果。



---

# 參考來源

參考網路教學文章：[Profiles REST API](https://github.com/LondonAppDeveloper/byob-profiles-rest-api) -- REST API providing basic functionality for managing user profiles.
