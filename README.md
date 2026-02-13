# IT-Incident-Agent-
This AI agent takes a messy IT incident report and turn it into the screen incident ticket that is ready to log

I have outlined some of the TOOLS with probabale inputs output data types.

1.signal_extractor tool:
	Input - Entire User complaint text.
	Output - Json schema with following set of mandatory(M) and optional(O) fields.
		a. Symtoms - String - M [ What are the effects of the failure, eg: Unable to browse, Not able to access etc ]
		b. service/app - String - M [ Which application or the service is failing ]
		c. Start time - TimeDate - M [ Extract the timestamp from the user complaint ]
		d. Scope - String - M [ single user / multiple / all users ]
		e. Environment - String - M [device/OS/app/browser/network ]
		f. Errors - String - O [ Any error message listed as part of the user complaint ]
		g. Impact - String - M [ business impact ]
		
2. category_classifier tool:
	Input - Json schema generated during perceive stage.
	        Use the Json fileds "service/app" "Environment" and "Errors" fields to arrive at the Category.
	Output - One of the following four Categories.
		 a. Network & Connectivity (VPN/Wi-Fi/DNS)
		 b. Access & Authentication (SSO/MFA/Login/Token/Permissions)
		 c. App Errors & Crashes (bugs, crashes, error messages)
                 d. Performance & Availability (slow, timeouts, 5xx, outage/degradation)

3. priority_rules_engine tool:
	Input - Json schema generated during perceive stage.
		Use the Json fields "service/app" "Scope" and "Impact" fields to arrive at the Priority.
	Output - One of the three Priorities [ P0 - High, P1 - Medium, P2 - Low ]
	
4. ticket_writer tool:
	Input - Json schema generated during perceive stage.
		Use all the fields.
	Output - Use the all the Json schema fields to arrive at a crisp Tile of the issue and
		 Write an appropriate and prescise write up (4 to 6 lines) to describe the user complaint in detail.
		 
5. missing_fields_checker tool:
	Input - Json schema generated during perceive stage.
	Output - Check if all the mandatory fields are intact.
		 If NOT: Write a crisp query to the user asking for the missing fields. (We may need a promt for this).
		 
		 
