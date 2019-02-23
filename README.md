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


-------------------1)Query for all incidents with a category of ‘hardware’…log a count of records returned------------------


var ga=new GlideRecord('incident');
ga.addQuery('category','hardware');
ga.query();
while(ga.next()){
gs.print('Number = ' + ga.number);
}

gs.log('Total count of the incident with category as hardware is ' + ga.getRowCount());


----------------- 

---------------------------------6)Abort the submission of any incident with high priority--------------------------------

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


---------------3)4)Creating incident through script with specific condition also--------------------------------------------

var gr=new GlideRecord('incident');
gr.initialize();
gr.setDisplayValue('caller_id','Joe Employee');//This is the format for reference type field. Direct not allowed.
gr.impact= 1 ;//Inorder to create a priority 1 incident you have to mention impact and urgency values.Direct value not allowed
gr.urgency=1;
gr.description= "Creation through script";
gr.setDisplayValue('cmdb_ci','*ANNIE-IBM');
gr.insert();
gs.print("incident created successfully");


----------------1)Query for ITIL user record--------------------------------------------------------


Script Include-

fn_counttotal : function(){

		var input=this.getParameter("sysparm_val");
		var inputstr=input.toString();
		
		var arr_q=[];


		var count = new GlideAggregate('task'); count.addEncodedQuery('ref_incident.caller_id=' + input + '^sys_class_name=incident^NQsys_class_name=change_request^ref_change_request.requested_by=' + input + '^NQsys_class_name=problem^opened_by=' + input);
		count.addAggregate('COUNT','sys_class_name');
		count.query();
		while(count.next()){
			arr_q.push(count.getValue('sys_class_name') +" = " + count.getAggregate('COUNT','sys_class_name'));
		}
		return arr_q.join("\n");


	}

});






Write onChange client script on incident, problem and SR and call the script include function.


function onChange(control, oldValue, newValue, isLoading, isTemplate) {
   if (isLoading || newValue === '') {
      return;
   }
var ga=new GlideAjax('TotalCount_SI2');
 ga.addParam("sysparm_name","fn_counttotal");
 ga.addParam("sysparm_val",newValue);
 ga.getXML(my_fun);
function my_fun(response){
alert(response);
}



------------------------Set up a department on your user record. Use a UI action to show an information message with the sys_id of your department.-----------------------------



Create a UI Action in the user table and include in the script 

gs.addInfoMessage('The department of the user is ' + gs.getUser().getDepartmentID());



-----------------------------Use a client script or UI policy to display an alert message showing the title of the currently logged-in user.-----------------------------


UI policy

gs.getUser().getRecord().getValue('title');



---------Set yourself up with some roles and test (in both a client script and a business rule) to see if you have that role----------



function onLoad() {
   //Type appropriate comment here, and begin script below
   if(g_user.hasRoles('admin','itil'))
g_form.setValue('description',"Contains required roles");
}


Also through Server side scripting.



-----------------Disallow ‘itil’ users the ability to read any record where the state is ‘Closed’. Hint: You shouldn’t use an ACL for this one!-----------------------------


Write Before Query Business Rule. Dont mention any table.

if (gs.hasRole("itil") && gs.isInteractive()) {
  var qc = current.addQuery("state", '!=', '7');
}


Others are simple or can be found at community


g_form.setMandatory('state',true);
g_form.setVisible('state',false);
g_form.setReadOnly('state',true);

Also UI Policy

buttons are available
 Visible choose false.


-----------------------------Populate the manager,location of the caller user----------------------------------------

function onChange(control, oldValue, newValue, isLoading, isTemplate) {
var val=g_form.getValue('caller_id');
var user=new GlideRecord('sys_user');
user.addQuery('sys_id',val);
user.query();
	while(user.next()){
		g_form.setValue('u_manager_name',user.manager);
		g_form.setValue('u_manager_email',user.email);
		g_form.setValue('location',user.location);
	}

	

   //Type appropriate comment here, and begin script below
   
}

-----------Stop the submission of any Priority 1 incident created by a user without the ‘itil’ role. Alert the user when this happens---

function onSubmit() {
   //Type appropriate comment here, and begin script below
 if(g_user.hasRole('itil') && g_form.getValue('priority')==1){
 alert("Record submission aborted");  
g_form.submitted=false;
return false;
}}

function onSubmit() {
   //Type appropriate comment here, and begin script below
   if(g_user.hasRole('itil','itil_admin')&& g_form.getValue('priority')==1)
   
return confirm('Submit a priority 1??');

}


-------------------------Resend an email----------------

Go to email logs and change the view to Sent view by clicking on Hamburger icon.
Click on any of the records. Now click on the Resend Button.

----------------------Trigger an event from graphical workflow that sends an email.---------
--------------Set up an end-to-end email notification using a business rule trigger.


------------------Write a schedule job to update the description from test to testing schedule job--------------

In the script section of the schedule job:-

var ga=new GlideRecord('problem');
ga.addQuery('description','test');
ga.query();
while(ga.next()){
	ga.description='Schedule job Testing';
	ga.update();
}


------------------------------schedule job to update the field name to'hardware'----------------------

var gr=new GlideRecord('incident');
gr.addEncodedQuery('category=software^active=true');
gr.query();
while(gr.next()){
	gr.category='hardware';
	gr.update();
}


-------------------------------Mandatory Attachment before order the item--------------------------------

OnSubmit client script  (It will have performance issues)

function onSubmit() {
   //Type appropriate comment here, and begin script below
 var att = new GlideRecord("sys_attachment");
  att.addQuery("table_name", "sc_cart_item");
  //att.addQuery("sys_created_by", g_user.userName);
  //att.addQuery("sys_created_on", '>=', 'javascript:gs.minutesAgo(10)');
  att.query();
  if (!att.next()) {
    confirm("Please attach the requested form before submitting.");
    return false;
  }
  return true;
}


----------------------------------Script to count the total number of incidents in active state-----------------

var count = new GlideAggregate('incident');
count.addQuery('active','true');
count.addAggregate('COUNT');
count.query();

while(count.next()){
var inccount=count.getAggregate('COUNT');
gs.log=("There are currently " + inccount +"incidents");

}


------------------------------------Script to divide the total count of incidents based on category---------------------


var ga=new GlideAggregate('incident');
ga.addQuery('active','=','true');
ga.addAggregate('COUNT','category');
ga.query();

while(ga.next()){

var cat=ga.category;
var category_count=ga.getAggregate('COUNT','category');

gs.log("The total count of " + cat + "is :" + category_count);
}


---------------------------------------Get User Details-----------------------------------------------------------

var user_obj=gs.getUser();         //Retrieving the user object 
gs.print(user_obj.getFirstName()); //Retrieving the user first name(o/p-System for System Administrator)
gs.print(user_obj.getLastName());  //Retrieving the user last name(o/p- Administrator for System Administrator)
gs.print(user_obj.getID());        //Retrieving the User ID (o/p- sysid)

var user_obj=gs.getUserName();     //Retrieves username
gs.print(user_obj);

gs.print(user_obj.isMemberOf('CAB Approval'));  // Returns false


----------------------------------------Adding a role to all users--------------------------------------------------

Write a background script:-

var gr = new GlideRecord("sys_user");
gr.query();
while(gr.next()) {
var userrole = new GlideRecord("sys_user_has_role");
userrole.initialize();
userrole.role = "282bf1fac6112285017366cb5f867469" ;  //Pass the sys_id of the role you want to add to the user
userrole.user = gr.sys_id;
userrole.insert();
}


----------------The below code will not add the role to the user if the role is already present for the user for the above scenario-----------------------

var gr = new GlideRecord("sys_user");
gr.query();
while(gr.next()) {
var role = new GlideRecord('sys_user_has_role')
role.addQuery('user',gr.sys_id);
role.addQuery('role', '<sys_id of the role>');
role.query();
if(!role.next())
{
role.initialize();
role.user = gr.sys_id;
role.role = "<sys_id of the role>";
role.insert();
}
}
































