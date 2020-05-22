1. Simple Multiprocessing.
~~~python
import multiprocessing
import os

## creating some function to run in parallel
def print_cool(a):
    print("cool {}".format(a))
    print("process id {}".format(os.getpid()))

def print_rule(b):
    print("rule {}".format(b))
    print("process id {}".format(os.getpid()))

# creating multiple process
p1 = multiprocessing.Process(target=print_cool, args("cool it is"))
p2 = multiprocessing.Process(target=print_rule, args("ok it is"))

# Making process run.
p1.start()
p2.start()

# check process still working.
print("Process p1 alive ? {}".format(p1.is_alive()))
print("Process p2 alive ? {}".format(p2.is_alive()))

# make current process to wait for p1 and p2.
p1.join()
p2.join()

~~~

2. Sharing data with global process.
~~~python
import multiprocessing

def add_sum(cool_list, result, val_sum):
    
    for idx, num in enumerate(cool_list):
        result[idx] = num*idx

    val_sum.value = sum(result)

if __name__ == "__main__":
    mylist = [1,2,3,4]
    
    # creating an ctype array that can be shared across multiple process.
    result = multiprocessing.Array('i', 4) # 'i' indicate integer type, 'd' indicate float.

    # Creating an value that can be shared across multiple process.
    val_sum = multiprocessing.Value('i')

    # Creating new process.
    p1 = multiprocessing.Process(target=add_sum, args=(my_list, result, val_sum))

    # starting the process.
    p1.start()

    # ending the process.
    p1.join()

    print(" the sum {}, and they array {}".format(result, val_sum[:]))
~~~
3. Mutli-processing through Server Process.
   1. Act as a manager, Create new process on request.
   2. Work with python type such as lists, dictionary.
   3. Can hold Python objects and allow other process to manipulate them. 
   4. A single manager can be used across network on multiple devices. However slower.
   
~~~python

import multiprocessing
def print_records(records):
    for record in records:
        print("name : {}, score : {}".format(record[0],record[1]))
    
def insert_record(record, records):
    records.append(record)

if __name__ == "__main__":
    # creating with manager as scope.
    with multiprocessing.Manager() as manager:

    records = manager.list([("cool",21),("real",23)])
    new_rec = ("chow",45)

    ## creating process for writing the record.
    p1 = multiprocessing.Process(target=insert_record,args=(new_rec,records))

    ## creating the process to display the result.
    p2 = multiprocessing.Process(target=print_records, args=(records))

    # starting the first and then waiting for it to finish.
    p1.start()
    p1.join()

    # running second after making sure first is run.
    p2.start()
    p2.join()

~~~
4. Queue Example.
~~~python
import multiprocessing 
  
def square_list(mylist, q): 
    """ 
    function to square a given list 
    """
    # append squares of mylist to queue 
    for num in mylist: 
        q.put(num * num) 
  
def print_queue(q): 
    """ 
    function to print queue elements 
    """
    print("Queue elements:") 
    while not q.empty(): 
        print(q.get()) 
    print("Queue is now empty!") 
  
if __name__ == "__main__": 
    # input list 
    mylist = [1,2,3,4] 
  
    # creating multiprocessing Queue 
    q = multiprocessing.Queue() 
  
    # creating new processes 
    p1 = multiprocessing.Process(target=square_list, args=(mylist, q)) 
    p2 = multiprocessing.Process(target=print_queue, args=(q,)) 
  
    # running process p1 to square list 
    p1.start() 
    p1.join() 
  
    # running process p2 to get queue elements 
    p2.start() 
    p2.join() 
~~~
