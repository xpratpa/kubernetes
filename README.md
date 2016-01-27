#Getting Started

Before you begin : 

If you're looking for a Docker Compose Solution instead of Kubernetes, Please check it out here : https://github.com/cloudboost/docker

Replace [<Node_IP>] with your cluster physical Node IP's

**Step 1 :**

Run `kubectl create -f cloudboost.yaml`

**Step 2 :**

Delete Redis Master Pod

Run `kubectl delete pods redis-master` for Redis to elect a new master and replication to happen. 

**Step 3 :**

Setup MongoDB Replication

TODO : Create a headless service to discover the nodes automatically and initialize a replicaset. 
Check :  
http://stackoverflow.com/questions/34207986/how-do-you-set-up-mongo-replica-set-on-kubernetes
https://groups.google.com/forum/#!topic/coreos-user/-g3o3ZAOwQ4

But for now, We can. 

Get all the MongoDB pod IP `kubectl describe pod <PODNAME> | grep IP | sed -E 's/IP:[[:space:]]+//'`

and...

Run `kubectl exec -i <POD_1_NAME> mongo`

and ... 

rs.initiate({ 
     "_id" : "cloudboost", 
     "version":1,
     "members" : [ 
          {
               "_id" : 0,
               "host" : "<POD_1_IP>:27017",
               "priority" : 10
          },
          {
               "_id" : 1,
               "host" : "<POD_2_IP>:27017",
               "priority" : 9
          },
          {
               "_id" : 2,
               "host" : "<POD_3_IP>:27017",
               "arbiterOnly" : true
          }
     ]
});

For Example :  

rs.initiate({  
     "_id" : "cloudboost",
     "version":1,
     "members" : [ 
          {
               "_id" : 0,
               "host" : "10.244.1.5:27017",
               "priority" : 10
          },
          {
               "_id" : 1,
               "host" : "10.244.2.6:27017",
               "priority" : 9
          },
          {
               "_id" : 2,
               "host" : "10.244.3.5:27017",
               "arbiterOnly" : true
          }
     ]
}); 

Please Note : IP's can be different for your cluster. 

Read from MongoDB Slaves 

`rs.slaveOk()`

#Todo

- Move MongoDB Repliaset initialization to a headless service. 
- Add Auth to MongoDB







