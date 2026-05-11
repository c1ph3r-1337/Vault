index=main

error

source="brute.log" STATUS=success

source="brute.log" STATUS=failed

source="brute.log" STATUS=failed 
| timechart span=1d count 
| sort by count

source="brute.log" STATUS=failed 
| stats count by host 
| sort - count

source="brute.log" STATUS=failed REASON=bad_password
| stats count by SRC
| sort - count