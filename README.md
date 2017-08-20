
## 1. Algorithm Question
```
class Node{
    Node next;
    int value;
    Node(int val){
        value = val;
    }
}

public class reverse {


    // Iterative way
    // Time complexity O(n), Space Complexity O(1) , n is the number of node in the list.
    public Node reverseList1(Node head){
        Node curr = head;
        Node pre = null;
        while(curr != null){
            Node next = curr.next;
            curr.next = pre;
            pre = curr;
            curr = next;
        }
        return pre;
    }

    // Recursion way
    // Time complexity O(n), Space Complexity O(n) , n is the number of node in the list.
    // The extra space comes from the stack space in each recursion level.
    public Node reverseList2(Node head){
        if (head == null || head.next == null) return head;
        Node pre = reverseList2(head.next);
        head.next.next = head;
        head.next = null;
        return pre;
    }

    // print result
    public static String print(Node head){
        String res = "";
        while(head != null) {
            res += head.value + "->";
            head = head.next;
        }
        return res + "null";
    }


    public static void main(String[] args) {
        reverse i = new reverse();

        // test case 1:  1->2->3->null
        // result 3->2->1->null
        Node test1 = new Node(1);
        test1.next = new Node(2);
        test1.next.next = new Node(3);
        System.out.println(print(i.reverseList1(test1)));

        // test case 2:  1->null
        // result 1->null
        Node test2 = new Node(1);
        System.out.println(print(i.reverseList1(test2)));
    }
}
```

## 2. Design Pattern question

### problem
we are supposed to deal with the distribution of millions of requests and now we have many servers to process these requests concurrently.


Because the requests of the clients' need consolidated distribution and we need keep the uniqueness of load balancer, which means that we can only have one load balancer to take the responisbility of managing servers and request distribution.

Therefore, we decide to design a load balancer following the singleton design pattern to process these requests.

### Code

#### Lazy Initilization.

The following implementation can keep the uniqueness of the instance and it also avoided creating instance before it is being used. 

However, it only works fine in the single thread environment.
```

import java.util.*;

public class LoadBalancer {

    private static LoadBalancer instance = null;
    //serverl list
    private List serverList = null;

    //private constructor
    private LoadBalancer() {
        serverList = new ArrayList();
    }

    //gets the instance through static initialization
    public static LoadBalancer getLoadBalancer() {
        if (instance == null) {
            instance = new LoadBalancer();
        }
        return instance;
    }

    //add server
    public void addServer(String server) {
        serverList.add(server);
    }

    //remove server
    public void removeServer(String server) {
        serverList.remove(server);
    }

    //get server with random selection method.
    public String getServer() {
        Random random = new Random();
        int i = random.nextInt(serverList.size());
        return (String)serverList.get(i);
    }
    public static void main(String[] args) {
        LoadBalancer balancer1 = instance.getLoadBalancer();
        LoadBalancer balancer2 = instance.getLoadBalancer();
        System.out.println(balancer1.hashCode());
        System.out.println(balancer2.hashCode());

        balancer1.addServer("1");
        balancer1.addServer("2");
        balancer1.addServer("3");
        balancer1.addServer("4");

        System.out.println(balancer2.getServer());


    }
}
```


#### Thread safe singleton.

The following implementation can make the global access synchronized, each time only one thread can access this method.

However, the speed is not fast for the first time access.
```

import java.util.*;

public class LoadBalancer {

    private static LoadBalancer instance = null;
    //serverl list
    private List serverList = null;

    //private constructor
    private LoadBalancer() {
        serverList = new ArrayList();
    }

    //gets the instance through static initialization
    public static LoadBalancer getLoadBalancer() {
        if (instance == null) {
            synchronized(LoadBalancer.class){
                if(instance == null){
                    instance = new LoadBalancer();
                }
            }
        }
        return instance;
    }

    //add server
    public void addServer(String server) {
        serverList.add(server);
    }

    //remove server
    public void removeServer(String server) {
        serverList.remove(server);
    }

    //get server with random selection method.
    public String getServer() {
        Random random = new Random();
        int i = random.nextInt(serverList.size());
        return (String)serverList.get(i);
    }
    public static void main(String[] args) {
        LoadBalancer balancer1 = instance.getLoadBalancer();
        LoadBalancer balancer2 = instance.getLoadBalancer();
        System.out.println(balancer1.hashCode());
        System.out.println(balancer2.hashCode());

        balancer1.addServer("1");
        balancer1.addServer("2");
        balancer1.addServer("3");
        balancer1.addServer("4");

        System.out.println(balancer2.getServer());


    }
}
```

#### Note
In the main function, the balancer1 and balancer2 are using the same instance. we can check their hashcode to confirm.

Also, the balancer1 added servers, balancer2 can get servers even it never added.


## 3. Optional questions

### 1. what is  session of Hibernate

Session is the main interface which is used for the interaction between Java Application program and Hibernate. Generally, Session is used to get a physical connection with a database. 

The Session object is lightweight and has its lifecycled and esigned to be instantiated each time an interaction is needed with the database. 

Persistent objects are saved and retrieved through a Session object.

### 2. How would you control transaction with Spring & Hibernate. Answer with different scenarios.

Without spring, in Hibernate, each time we finish a operation, we need to start a transaction, then do the data operation and summit the transaction, finally, close the transaction.

When we integrate spring with Hibernate, we don't need to open transaction and summit transaction every time we want to finish a data operation, we only need the HibernateTemplate provided by Spring. 
We only need the data operation method in the class.

Also, Spring provided the transaction management for Hibernate. With Spring, we use anotation like @Transaction to manage the transaction and use rollback for exception. 
