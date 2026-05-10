source="brute.log" STATUS=success

source="brute.log" STATUS=failed

source="brute.log" STATUS=failed 
| timechart span=1d count 
| sort by count