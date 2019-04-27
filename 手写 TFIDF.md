## 手写 TFIDF

````python
import math
# 词在文章中出现次数 / 该文章总词数
def tf(word, doc):
    count = sum(1 for w in doc if w.strip()==word.strip())
    return count / len(doc)
# log(总文档数 / (包含该词文档数+1))
def idf(word, docs):
    count = sum(1 for doc in docs if word in doc)
    return math.log(len(docs) / (1 + count))
# tf*idf
def tfidf(word, doc, docs):
    return tf(word, doc) * idf(word, docs)
# tfidf矩阵
def tfidf_metrix(docs=[]):
    s = set(word for doc in docs for word in doc)
    res = list([tfidf(word,doc,docs) for word in s] for doc in docs)
    return res

def addDoc(docs,doc):
    docs.append(doc)
    
docs=[]
doc1 = ['a','b','c']
doc2 = ['d','e','f']
doc3 = ['g','h','i','j']
doc4 = ['g','h','i','j','k','m','n']
addDoc(docs,doc1)
addDoc(docs,doc2)
addDoc(docs,doc3)
addDoc(docs,doc4)

np.array(tfidf_metrix(docs))
````
运行结果
````python
array([[0.        , 0.        , 0.        , 0.23104906, 0.        ,
        0.23104906, 0.        , 0.        , 0.        , 0.23104906,
        0.        , 0.        , 0.        ],
       [0.        , 0.        , 0.23104906, 0.        , 0.23104906,
        0.        , 0.        , 0.        , 0.        , 0.        ,
        0.23104906, 0.        , 0.        ],
       [0.07192052, 0.        , 0.        , 0.        , 0.        ,
        0.        , 0.07192052, 0.        , 0.        , 0.        ,
        0.        , 0.07192052, 0.07192052],
       [0.04109744, 0.09902103, 0.        , 0.        , 0.        ,
        0.        , 0.04109744, 0.09902103, 0.09902103, 0.        ,
        0.        , 0.04109744, 0.04109744]])
````

