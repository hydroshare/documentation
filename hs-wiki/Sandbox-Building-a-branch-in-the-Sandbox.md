You can do an automated build of your branch in https://jenkins.renci.org. This will use http://sandbox.hydroshare.org

The status will show up in the hydoshare_builds hipchat room (https://hydroshare.hipchat.com/rooms/show/1543541)

Replace CHECKOUT_BRANCH in the following URL: 


`https://jenkins.renci.org/buildByToken/buildWithParameters?job=build-sandbox-pull-request/&token=sandbox&CHECKOUT_BRANCH=jenkins_dev&CLEAN_DATABASE=true`


*    CHECKOUT_BRANCH = branch to checkout
*    CLEAN_DATABASE = setup a clean database. If this is the first time testing a branch, use true, otherwise use false.


Two users are automatically created, and used for testing scripts which will run after the build:
* viewer
* creator

Several generic resources will be added to the sandbox during testing.


