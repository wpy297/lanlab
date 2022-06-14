Lab3:  
=======================================  
Introduction  
--------------------------------------  
- 了解存储库的模式。  

- 了解服务层模式。  

- 理解为什么将业务逻辑与数据存储技术分离起来很重要  

Material and Methods  
-----------------------------------  
2.1 工具  

2.2 方法 

Result  
-----------------------------------------  
3.1 repository.py  

代码  

.. code::  

 import abc
 import model
 import os,pickle


 class AbstractRepository(abc.ABC):
    @abc.abstractmethod
    def add(self, batch: model.Batch):
        raise NotImplementedError

    @abc.abstractmethod
    def get(self, reference) -> model.Batch:
        raise NotImplementedError


 class SqlAlchemyRepository(AbstractRepository):
    def __init__(self, session):
        self.session = session

    def add(self, batch):
        self.session.add(batch)

    def get(self, reference):
        return self.session.query(model.Batch).filter_by(reference=reference).one()

    def list(self):
        return self.session.query(model.Batch).all()

 class PickleRepository(AbstractRepository):
    ''' Complete the definition of this class. '''
    def __init__(self, path = None):
        self.path = path

    def add(self, batch):
        f = open(self.path, 'wb')   #以二进制格式写入文件
        pickle.dump(batch, f)       #将 Python 中的对象序列化成二进制对象，并写入文件
        f.close()

    def get(self, reference):
        result = []
        batches = list(self.path)   #获取pickle文件中所有Batch对象
        if batches:
            for batch in batches:
                if batch.reference == reference:  #查找所有符合对象
                    result.append(batch)     
        return result

    def list(self):
        if os.path.getsize(self.path) > 0:  #判断文件是否为空
            f = open(self.path, 'rb')       #以二进制格式读取文件
            batches = []
            while 1:        #读取未知数量的 pickle 对象，反复 load 文件对象，直到抛出异常为止
                try:
                    batches.append(pickle.load(f))  #读取指定的序列化数据文件，并返回对象
                except EOFError:
                    break
            f.close()
            return batches



Discussion
-----------------------------------


Reference
-------------------------------------
