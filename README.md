# DynomiteDB persistent backends (based on ARDB)

DynomiteDB is a framework that turns single server databases into scalable, distributed databases.

The DynomiteDB key/value API is RESP (REdis Serialization Protocol) compatible. In other words, this means that you can use any Redis client (including `redis-cli`) to interact with DynomiteDB.

ARDB provides a RESP compatible API to Facebook RocksDB, Google LevelDB, WiredTiger and LMDB.

The combination of Dynomite with ARDB means that you can use any Redis client to develop scalable applications using your existing Redis knowledge.

Specifically, the combination of Dynomite and ARDB means that you can use the same Redis API that you already know for a variety of workloads, including:

- High performance in-memory workloads (in-memory)
- Big data cache (in-memory)
- Mixed read/write workloads (on disk)
- Read-heavy workloads (on disk)
- High frequency write workloads (on disk)
- Real-time analytics (in-memory)

This repo is a fork of [ARDB](https://github.com/yinqiwen/ardb)], a BSD licensed RESP compatible server.

## Database storage engines

DynomiteDB ARDB supports the following database engines:
 
- [Facebook RocksDB](https://github.com/facebook/rocksdb)
- [LMDB](http://symas.com/mdb/)
- [Google LevelDB](https://github.com/google/leveldb)
- [MongoDB WiredTiger](http://www.wiredtiger.com/)

A database storage engine is the final layer that a database management system (DBMS) uses to write and read your data to/from disk. Specifically, every database uses a database storage engine to persist your data.

Traditional database systems have tightly integrated storage engines which limits the types of workloads that a database handles effectively. DynomiteDB, on the other hand, allows you to choose between different storage engines which means that you can use the same Redis API across a wide variety of workloads.

<table>
	<tr>
		<th>Storage engine</th>
		<th>Workload (use case)</th>
	</tr>
	<tr>
		<td>LMDB</td>
		<td>
			<ul>
				<li>Exceptionally fast with &lt; 30M keys</li>
				<li>Use with a higher number of smaller servers for optimal performance</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>RocksDB</td>
		<td>
			<ul>
				<li>High performance with 100M keys</li>
				<li>Good for write heavy workloads within limits</li>
				<li>Uses compaction so don't use for <u>exceptionally high frequency</u> write workloads</li>
				<li>Use instead of LevelDB when using SSDs</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>LevelDB</td>
		<td>
			<ul>
				<li>Minimizes disk utilization (i.e. small size on disk)</li>
				<li>Works well with hard drives (i.e. spinning disks)</li>
				<li>Use instead of RocksDB on hard drives</li>
			</ul>
		</td>
	</tr>
	<tr>
		<td>WiredTiger</td>
		<td>
			<ul>
				<li>Good choice for MongoDB users who want to use the same storage engine</li>
				<li>Provides both Btree and LSM based tables</li>
				<li>Use LSM for write heavy workloads</li>
				<li>Use BTree for read heavy or mixed workloads</li>
			</ul>
		</td>
	</tr>
</table>

If you are unsure which of the storage engines is best for you, then we recommend that you test each against your workload. With DynomiteDB you can quickly install all of the storage engines and run a benchmark using your own data to determine which performs best for you.

<!--
[![Build Status](https://travis-ci.org/yinqiwen/ardb.svg?branch=master)](https://travis-ci.org/yinqiwen/ardb)  
-->

## Install prerequisites

### Ubuntu

```bash
sudo apt-get install build-essential autoconf libsnappy-dev
```

## Clone the repo

```bash
mkdir ~/repos && cd $_ && git clone https://github.com/DynomiteDB/ardb.git && cd ardb
```

## Compile

LMDB is the default storage engine. To compile ARDB with LMDB just type `make`.

Set the value of `storage_engine` before calling `make` to build using a different storage engine.

The following binaries will be build in the `src` directory:
- `ardb-server`
- `ardb-test`

### Compile the LMDB backend

```bash
make
```

### Compile the RocksDB backend

```bash
storage_engine=rocksdb make
```

### Compile the LevelDB backend

```bash
storage_engine=leveldb make
```

### Compile the WiredTiger backend

```bash
storage_engine=wiredtiger make
```

## Features

- Full redis-protocol compatibility
- 2d spatial index supported. [Spatial Index](https://github.com/yinqiwen/ardb/blob/master/doc/spatial-index.md)
- Most redis commands supported, and a few new commands
  * [Ardb commands VS Redis Commands](https://github.com/yinqiwen/ardb/wiki/ARDB-Commands)
- Different storage engine supported (LevelDB/LMDB/RocksDB)
- Lua Scripting support 
- Backup data online
  * Use 'save/bgsave' to backup data
  * Use 'import' to import backup data
  * [Backup & Restore](https://github.com/yinqiwen/ardb/wiki/Backup-Commands)

## Clients

You can use any <a href="http://www.redis.io/clients" target="_blank">Redis client</a> to connect to DynomiteDB.

Java developers should use the <a href="https://github.com/Netflix/dyno" target="_blank">Dyno client</a>.

* **Known Issues**:   

  - For Node.js, the recommand client [node_redis](https://github.com/mranney/node_redis) would try to parse `redis_version:x.y.z` from `info` command's output, Ardb users should uncomment `additional-misc-info` in ardb.conf to makesure that `redis_version:x.y.z` exists in `info` command's output. There is an online redis GUI admin service [redsmin](https://redsmin.com) build on [node_redis](https://github.com/mranney/node_redis), users can test ardb's redis protocol conformance by a visual way. 
  
## Benchmark

Benchmarks for DynomiteDB with each database storage engine is in progress. Benchmark results will be published on http://www.dynomitedb.com.

## ARDB vs Redis (2.8.9) 

- Unsupported Redis Commands:
	- DUMP 
	- MIGRATE
	- OBJECT
	- RESTORE
	- CONFIG RESETSTAT
	- DEBUG
	- MONITOR
	- BITPOS
	- PUBSUB
- New ARDB Commands:
	- SUnionCount/SInterCount/SDiffCount
	- HMIncrby
	- CompactDB/CompactAll
	- BitOPCount
	- Import
	- KeysCount
	- GeoAdd
	- GeoSearch
	- Cache 

