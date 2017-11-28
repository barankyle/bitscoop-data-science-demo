# 4. Deploy code to Amazon Lambda, Explore data with Quicksight
This should take care of all of the networking requirements.
You will now need create a new Lambda function with the project code.

Go to [Lambda Home](https://console.aws.amazon.com/lambda/home) and click ‘Create a Lambda function’.
For the blueprint select ‘Blank Function’.
For the trigger, click the gray dashed box and select ‘Cloudwatch Events - Schedule’.
Name the trigger whatever you want.
The schedule expression can be whatever you want as well; ‘cron(0 17 ? * MON-SUN *)’ will run the function once a day at 17:00 UTC.
The documentation for schedule expressions can be found [here](http://docs.aws.amazon.com/lambda/latest/dg/tutorial-scheduled-events-schedule-expressions.html).

Name the function something and set the runtime to Node.js 6.10.
For Code Entry Type click the dropdown and select ‘Upload a .ZIP file’, then click on the Upload button that appears and then navigate to and select the bitscoop-data-science-demo-<version>.zip file.
The Handler should be ‘index.handler’.
For the role, select the role that you created earlier.

You will need to add several Environment Variables, with the number depending on how many services you wish to run. You will always need to add the following variables:

* BITSCOOP_API_KEY (obtainable at https://bitscoop.com/keys)
* PORT (by default it’s 3306)
* HOST (the endpoint for the RDS box, <Box name>.<ID>.<Region>.rds.amazonaws.com)
* USER (the username you picked for the RDS box)
* PASSWORD (the password you set for the RDS box)
* DATABASE (the database name you set for the RDS box)

If you are getting data from GitHub, you will need to add these:

* GITHUB_MAP_ID (The ID of the BitScoop API map you created for GitHub)
* GITHUB_USER (the username that owns the repo you’re interested in)
* GITHUB_REPO (the name of the repo you’re interested in)

If you are getting data from Google Analytics, you will need to add these:

* GOOGLE_MAP_ID (The ID of the BitScoop API map you created for Google)
* GOOGLE_GA_VIEW_ID (the ID of the View you are monitoring)
* GOOGLE_CONNECTION_ID (the ID of the BitScoop Connection you created to the Google Analytics API Map)

If you are getting data from StatusCake, you will need to add these:

* STATUSCAKE_MAP_ID (The ID of the BitScoop API map you created for StatusCake)
* STATUSCAKE_TEST_ID (the ID of the Test you are monitoring)

If you are getting data from Postman, you will need to add these:

* POSTMAN_MAP_ID (The ID of the BitScoop API map you created for Postman)
* POSTMAN_MONITOR_ID (the ID of the Monitor you are running)

Open the Advanced Settings accordion.
You’ll probably want to set the Timeout to 10 seconds.
Under VPC select the ‘bitscoop-demo’ VPC.
For Subnets add the two ‘private’ subnets, and for the Security Group select ‘lambda’ one we created.
Hit next and you’ll be taken to a review screen, and then select ‘Create Function’ at the very bottom of the page.

If you did not set a trigger earlier, go to the details for the function and click the tab ‘Triggers’. Click ‘Add Trigger’, click on the dashed gray box, then ‘Cloudwatch Events - Schedule’. See above for the remaining steps to set up a scheduled trigger. Then click Submit.

Your Lambda function should be good to go.

## Testing the Lambda function

Click the ‘Actions’ dropdown next to ‘Test’ and select ‘Configure Test Event’.
Select the template 'Scheduled Event'.
The details of the test event are unimportant, so you can leave them as the defaults.

Click Save and Test and the function should run.

## Explore the data in AWS QuickSight

Congratulations! The BitScoop powered data science platform is now up and running. Let’s see what this data can tell us to help the data scientists at our imaginary company, Dog.Breed.

### Connect the Data to AWS QuickSight

After signing up for QuickSight, navigate to the Start page. Click New Analysis, then New Data Sets. You’ll want to create a new data set from a New Data Source, specifically RDS. Enter a name for this data source, then click on the down arrow on Instance and then click on the name of the RDS box. It should auto-populate the database name for you. Enter the username and password, then validate the connection. When that succeeds, click Create data source.

From there, select the table you want to analyze and click the Select button. By default QuickSight will have selected import to SPICE; leave it as is and click Visualize.

We will now create five charts to help determine the answers to the key questions we asked before. First select the “+ Add” button in the top left of Quicksight. Then select the “Visualize” bar on the right and ensure that the SPICE data set is chosen. Select the time field in blue to be mapped to the X axis of the visualization. Now you make select any column in order to visualize the different data sets together over time. The follow are the corresponding dimensions (columns vs time) used to answer each question from the data.

* Do product code releases relate to a change in site activity such as new users?
  * Chart code pushes, code releases, visitors, and new visitors over time.
* Do site traffic spikes relate to outages of the API?
  * Chart API test alerts and visitors over time.
* Do new user spikes relate to outages of the site?
  * Chart new visitors and site outage alerts over time.
* Do outages of the API relate to outages of the site?
  * Chart API outages and Site outages over time.
* Do outages of the API or relate to higher number of code commits?
  * Chart API outages and Code commits over time

### Exploring the data with other analysis products

Connecting other products to RDS, such as Tableau, DataIku, or Paxata, should be similar to connecting QuickSight. One thing you may have to differently is configure the RDS security group to accept incoming requests from whatever IP address or IP range this other service is calling from. The instructions above had you add QuickSight’s IP range to the RDS security group, but this is not what another service would be using.

Users are only beginning to explore what is possible on BitScoop Should you have any questions, don’t hesitate to reach out at https://bitscoop.com/support.
