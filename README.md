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


----------------------------------Date and Time------------------------------

var dt=new GlideDateTime("2018-09-07 08:00:00);
dt.addMonthsLocalTime(-2);
gs.info(dt.getDate());


var gdt=new GlideDateTime("2018-07-04 08:00:00);
gdt.addSeconds(1000);
gs.info(gdt.getValue());


rec.insert(),rec.deleteRecord()


-------------------Query for all incidents with a category of ‘hardware’…log a count of records returned--------------------

var ga=new GlideRecord('incident');
ga.addQuery('category','hardware');
ga.query();
while(ga.next()){
gs.print('Number = ' + ga.number);
}

gs.log('Total count of the incident with category as hardware is ' + ga.getRowCount());


----Query for all incidents created in the past 3 days where the caller is ‘Joe Employee’…log a count of records returned---

var ga=new GlideRecord('incident');
ga.addQuery('caller_id=681ccaf9c0a8016400b98a06818d57c7^sys_created_onRELATIVEGE@dayofweek@ago@3');
ga.query();
while(ga.next()){
gs.print('Number = ' + ga.number);
}

gs.log('Total count of the incident with category as hardware is ' + ga.getRowCount());

