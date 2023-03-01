# DP-203-Notes

## Distributing Data in Synapse Analytics

### Round-Robin Distributed

Data is distributed evenly in a random fashion

Best for: 
1) There is no clear distribution key
2) You don't have frequent joins with other tables
3) Uniform distribution 
4) Temporary staging table
5) A simple starting point

### Hash Distributed

Data is distributed deterministically by using a hash function

### Replicated

Full copy of the table is replicated to every node