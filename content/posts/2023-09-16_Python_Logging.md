---
title: "Python Logging 在多線程進階應用"
date: 2023-09-16T06:13:08+08:00
tags: ["知識","Python"]
categories: ["程式"]
draft: false
---

## 前言
最近工作上碰到自己的需求，想在定期專案中，對多線程中，同類型放同一個檔案，方便之後做trace code

因定期程式時間頻率高，一個工作內，還有併發，需做好log檔案管理，否則之後很難debug

## 實際問題
在APScheduler定時任務中，一個job預設是一個主線程任務，當其中一個主線程併發的子線程任務要分類回該主線程的紀錄檔案內，要如何做到？


# 主要程式邏輯
## 第一步
1.  對入口點建立logger
## 第二步
1. 進入主要線程，加入TimedRotatingFileHandler(線程安全)
2. 對主要線程命名，FileHandler檔案名稱
3. 退出主要線程時，關閉FileHandler
## 第三步
1. 在要併發的線程池，加上主要線程名稱當前綴

# 線程邏輯
對同類線程做重命名

# Logging邏輯
在FileHandler 加上filter，對線程名稱做分類檢查

在logging模組中，自定義Filter需繼承logging.Filter與實作介面filter，利用filter回傳true 或 false來判斷，這筆log record需不需要紀錄，
所以可以用把線程名稱當判斷依據，實作分類檢查的可能



    class LogUtil:    
        .....
        @classmethod
        def start_thread_logging(cls, log_path, file_name):
            thread_name = threading.Thread.getName(threading.current_thread())
            log_handler = cls.__get_file_handler(log_path, file_name)
            log_filter = ThreadLogFilter(thread_name)
            log_handler.addFilter(log_filter)
            logger = logging.getLogger()
            logger.addHandler(log_handler)
            return log_handler

        @classmethod
        def stop_thread_logging(cls, log_handler):
            logging.getLogger().removeHandler(log_handler)
            log_handler.close()


    class ThreadLogFilter(logging.Filter):
        def __init__(self, thread_name, *args, **kwargs):
            logging.Filter.__init__(self, *args, **kwargs)
            self.thread_name = thread_name
    
        def filter(self, record):
            return self.thread_name in record.threadName

    
    def main()
        log init ...
        APScheduler
        add_job..
        

    def job():
        thread_name = "job_name"
        threading.current_thread().name = thread_name
        log_handler = LogUtil.start_thread_logging(log_path=..., file_name=...)
        do_job(thread_name)
        LogUtil.stop_thread_logging(log_handler=log_handler)

    def do_job(thread_name):
        ...
        with ThreadPoolExecutor(thread_name_prefix=thread_name) as executor:
            for item in rows:
                executor.submit(work)
