https://jenkins.renci.org/job/SampleData_Restclient/

# Parameters:
*  	HYDROSHARE	
*  	Creator	
*  	CreatorPassword	

# REST
This can be called from the URL:
`https://jenkins.renci.org/buildByToken/buildWithParameters?job=SampleData_Restclient&token=sandbox&HYDROSHARE=dev.hydroshare.org&Creator={USER}&CreatorPassword={PASSWORD}`

# Notes:
Pulls from https://github.com/hydroshare/sampledata.git

runs tests in tests/api
