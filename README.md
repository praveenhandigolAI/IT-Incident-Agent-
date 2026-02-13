# IT-Incident-Agent-
This AI agent takes a messy IT incident report and turn it into the screen incident ticket that is ready to log

IT Incident Intake Agent: Triager + Ticket Draft (4-Category Version) 

Problem Context 

Modern IT teams receive incident reports through chat, email, ticket portals, and voice transcripts. These reports are often messy, incomplete, and inconsistent. Critical triage information such as impact scope, environment, start time, or error messages is frequently missing. 

Who faces the problem? 

• IT Service Desk teams, NOC teams, DevOps teams, and IT Operations managers. 

How is it solved today? 

• Manual reading, interpretation, follow-up questioning, and ticket rewriting by L1 support agents. 

What is slow, manual, expensive or error-prone? 

• Ticket normalization, category selection, priority assignment, and identifying missing mandatory fields are manual and inconsistent. Misclassification causes SLA breaches and escalation delays. 

One Primary Goal 

The agent’s goal is to convert unstructured IT incident messages into a structured, policy-compliant incident ticket, subject to organizational triage rules and 4-category taxonomy, while optimizing classification accuracy, SLA compliance, and triage speed. 

Scope and Boundaries 

What actions is the agent allowed to take? 

• Extract signals (symptoms, scope, impact, environment, timestamps) 

• Classify incident into exactly one of four categories 

• Assign priority (P0/P1/P2) using mandatory rules 

• Generate structured ticket title and summary 

• Identify missing mandatory fields 

• Ask up to 3 clarifying questions (only if needed) 

• Check duplicate titles (if recent ticket list is available) 

Where must it stop and ask a human? 

• When mandatory triage fields are missing 

• When scope (all users vs single user) is unclear 

• When environment (prod/test/dev) is not mentioned 

• When business impact cannot be inferred 

What decisions are out of scope? 

• No troubleshooting steps 

• No remediation suggestions 

• No workaround recommendations 

• No root cause analysis 

• Cannot override priority rules 

• Cannot auto-confirm major incident declaration 

• Cannot close or resolve tickets 

User Types 

Who interacts with the agent? 

• IT Service Desk Agents (L1) 

• IT Operations Engineers 

• Incident Managers 

• Automated Chat/Email Intake Systems 

How does the interaction begin? 

• User pastes a complaint message 

• System forwards email/chat transcript 

• Voice transcript converted to text and passed as input 

Formal Conversation Samples 

Conversation 1 – P0 Major Outage 

User Input: Production payment system is down. All users are getting 503 errors since 9:10 AM. Customers can’t complete transactions. 

Ticket Title: Production Payment Service Returning 503 for All Users 

Category: Performance & Availability 

Priority: P0 

Confidence: High 

Missing Fields: None 

Conversation 2 – P2 Single User Access Issue 

User Input: I can’t log into the HR portal. It keeps saying authentication token invalid. 

Ticket Title: User Unable to Log into HR Portal – Token Invalid Error 

Category: Access & Authentication 

Priority: P2 

Confidence: Medium 

Missing Fields: Environment, Start time, User location/team 

Conversation 3 – P1 Partial Degradation 

User Input: Multiple users reporting CRM is extremely slow since morning. Pages take 30–40 seconds to load. 

Ticket Title: CRM Application Experiencing Severe Slowness for Multiple Users 

Category: Performance & Availability 

Priority: P1 

Confidence: High 

Missing Fields: Exact start time, Environment 
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
		 
		 
