# NVDIMM Caching for MySQL 5.7

Optimize MySQL/InnoDB using NVDIMM 

## Publication
- Mijin An, Jonghyeok Park, Tianzheng Wang, Beomseok Nam, and Sang-Won Lee, "NV-SQL: Boosting OLTP Performance with Non-Volatile DIMMs", VLDB'23
- [Publication Link](https://dl.acm.org/doi/10.14778/3583140.3583159)

## Build and install

1. Clone the source code: 

```bash
$ git clone https://github.com/meeeejin/mysql-57-nvdimm-caching.git
```

2. Modify the `PASSWD` value in the build script:

```bash
$ vi build.sh

#!/bin/bash

BASE_DIR=`pwd -P`
BUILD_DIR=$BASE_DIR/bld
PASSWD="sudo-passwd"
...
```

3. Run the script file:

```bash
$ ./build.sh
```

The above command will compile and build the source code with the default option (i.e., caching new-orders and order-line pages). The available options are:

| Option     | Description |
| :--------- | :---------- |
| --origin   | No caching (Vanilla version)                        										 |
| --origin-monitor |  No caching but monitoring the flush status                                         |
| --nc       | Caching New-Orders and Order-Line pages (`default`) 										 |
| --nc-st    | Caching New-Orders, Order-Line and Stock pages                  							 |
| --nc-st-od | Caching New-Orders, Order-Line, Stock and Orders pages      					    		 |
| --mtr 	 | Caching New-Orders, Order-Line, Stock and Orders pages with mtr logging enabled           |

If you want the vanilla version, you can run the script as follows:

```bash
$ ./build.sh --origin
```

## Run

1. Add the following three server variables to the `my.cnf` file:

| System Variable                     | Description | 
| :---------------------------------- | :---------- |
| innodb_use_nvdimm_buffer            | Specifies whether to use NVDIMM cache. **true** or **false**. |
| innodb_nvdimm_buffer_pool_size      | The size in bytes of the NVDIMM cache. The default value is 2GB. |
| innodb_nvdimm_buffer_pool_instances | The number of regions that the NVDIMM cache is divided into. The default value is 1. |
| innodb_nvdimm_pc_threshold_pct      | Wakeup the NVDIMM page cleaner when this % of free pages remaining. The default value is 5. |
| innodb_nvdimm_home_dir				      | NVDIMM-aware files resident directory |

For example:

```bash
$ vi my.cnf
...
innodb_use_nvdimm_buffer=true
innodb_nvdimm_buffer_pool_size=2G
innodb_nvdimm_buffer_pool_instances=1
innodb_nvdimm_pc_threshold_pct=5
innodb_nvdimm_home_dir=/mnt/pmem
...
```

2. Run the MySQL server:

```bash
$ ./bld/bin/mysqld --defaults-file=my.cnf
``` 
