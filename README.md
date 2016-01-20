# tago-board-atmel-atwinc1500
Documentation for the Tago library used with atwinc1500 atmel board to use Tago's services.
Unless otherwise specified all the variables inside the library related to communication or configuration are of type string. 

STEP 1. Configuration of the Tago Service
In order to be able to communicate to the Tago service an account is required as well as a token. A token is the pass. Make sure you have at least one token available. More information under https://tago.io. 

First to do is the connection to your wifi network, for that you will have to:
a) create an object of type libTagoWincConfig_t
b) fill in the object as the example below, object name was given as config_wifi
  config_wifi.ssid  = "Your Network SSID";
  config_wifi.pswd  = "Your Network password";
  config_wifi.token = "Your Token";
  config_wifi.connType = CONNECTION_HTTP;		
The connection still can be (CONNECTION_HTTPS) in case your board does have Tago certification available (more information here).

With the fields set just call the method to start communication with the service.
  libTagoWinc__begin(&config_wifi);

STEP2. Exchanging data to Tago
To exchange data to the server an object of type libTagoWincPost_t is required to be configured. 

a) To send data to Tago
	Provide the variable name:
	  post_data.variable = "myvar";
	Give the value assigned to the variable:
	  post_data.value    = "11";
	Provide the unit associated (optional):
	  post_data.unit     = "volts";
	Provide the time associated (optional):
	  post_data.time     = "2015-11-03 13:44:33";
	Provide the call back function (required):
	  post_data.callback = mycallback;

In order to send the data a call to the insert function is required passing the above object as reference.
  libTagoWinc__insert(&post_data);
This function return 0 if it succeed on posting data. -1 if error or busy. 

b) To request data from Tago
	Provide the variable name:
	  find_data.variable = "myvar";
	Give the quantity required (optional, if not specified wil request just one):
	  find_data.qty    = "1";
	Provide the start date (optional, more formats at tago.io):
	  find_data.start_date = "2015-01-01";
	Provide the end date (optional, more formats at tago.io):
	  find_data.end_date = "2016-01-01";
	Provide the call back function (required):
	  find_data.callback = mycallback;

In order to request the data a call to the find function is required passing the above object as reference.
  libTagoWinc__find(&find_data);
This function return 0 if it succeed on posting data. -1 if error or busy. 

STEP 3. Keep connection
  A call to the method libTagoWinc__KeepConnection() is required in a loop to keep the connection in case an interrupt to wifi does happen.

STEP 4. Receiving and processing results (callback)
  The callback is a function passed from the application to the library. The function is executed as long as data are received from the Tago server after a request of (1) insert or (2) find. 

Callback in application shall be created as
  void mycallback(serverResponseObj_t *response);
  
The object contains preprocessed Json data response from the server. 

typedef struct {
	char * status;		/* true or false */
	char * fdbk_type;	/* post or get */
	char * message;		/* message from response */	
	char * variable;  /* variable name */
	char * origin;    /* variable origin */
	const char *unit; /* variable unit */
	char * time;      /* time variable was posted */
	const char *location_lat; /* latitude location (Not available yet) */
	const char *location_lng; /* longitude location (Not available yet) */
	char * value;     /* variable value */
	char * id;        /* variable number id */
} serverResponseObj_t;
