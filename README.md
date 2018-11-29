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


-----------------------------Delete all the incidents updated by 'ITIL User' in the past 24 hours---------------


var ga=new GlideRecord('incident');
ga.addQuery('sys_updated_onRELATIVEGE@dayofweek@ago@1^sys_updated_by=itil');
ga.query();
while(ga.next()){
ga.deleteRecord();
}

gs.print('Count= ' + ga.getRowCount());


---------------------------------Abort the submission of any incident with high priority--------------------------------

(function executeRule(current, previous /*null when async*/) {

	// Add your code here
if(current.priority==1){
	gs.addInfoMessage('Enter valid priority.P1 is not allowed');
	current.setAbortAction(true);
	
}
})(current, previous);


---------------------------------country state city combination in a Service Catalog--------------------------------------

Write an onChange script on Country

   if (isLoading || newValue == '') {
      g_form.clearOptions('state');
	   g_form.clearOptions('city');
	  
	  
   }
	else{
		g_form.clearOptions('state');
	if(newValue=='india'){
		g_form.addOption('state','Karnataka','Karnataka');
		g_form.addOption('state','Odisha','Odisha');
	}
	  else if(newValue=='usa'){
		g_form.addOption('state','Oklahoma','Oklahoma');
		g_form.addOption('state','Houston','Houston');
	  }
		else if(newValue=='aus'){
			g_form.addOption('state','NSW','NSW');
			g_form.addOption('state','Canberra','Canberra');
		}
	}
	
	
	Write an onChange script on State 
	
	if (isLoading || newValue == '') {
      g_form.clearOptions('city');
   }
	g_form.clearOptions('city');
if(newValue=='Odisha'){
	g_form.addOption('city','Bhadrak','Bhadrak');
	g_form.addOption('city','Balasore','Balasore');
	
}
	else if(newValue=='Karnataka'){
	g_form.addOption('city','Bengaluru','Bengaluru');
	g_form.addOption('city','Hubli','Hubli');
	
}
		else if(newValue=='Oklahoma'){
	g_form.addOption('city','SanDiego','SanDiego');
	g_form.addOption('city','Minneapolis','Minneapolis');
	
}
		else if(newValue=='Houston'){
	g_form.addOption('city','NYC','NYC');
	g_form.addOption('city','Colorado','Colorado');
	
}
	
		if(newValue=='NSW'){
	g_form.addOption('city','Wollongong','Wollongong');
	g_form.addOption('city','Perth','Perth');
	
}
		if(newValue=='Canberra'){
	g_form.addOption('city','Sydney','Sydney');
	g_form.addOption('city','Melbourne','Melbourne');
	
}


---------------Creating incident thorough script--------------------------------------------

var gr=new GlideRecord('incident');
gr.initialize();
gr.setDisplayValue('caller_id','Joe Employee');
gr.impact= 1 ;
gr.urgency=1;
gr.description= "Creation through script";
gr.insert();
gs.print("incident created successfully");






