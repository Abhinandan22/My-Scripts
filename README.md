# My-BS Scripts

-----------------------------Script to print the current date in a field incident form on loading the form--------------------------------------------

1 > Create a Display Business Rule 

(function executeRule(current, previous /*null when async*/) {

	// Add your code here
	
g_scratchpad.tdate=gs.now();
})(current, previous);

2 > Create an onLoad() client script 

function onLoad() {
   //Type appropriate comment here, and begin script below
   var date=g_scratchpad.tdate;
	g_form.setValue('u_today_s_date',date);
}


----------------------------------

