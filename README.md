#Meteor.js on OpenShift, with custom version o nodeJS
Deploy [meteor.js](http://meteor.com/) application bundles on [OpenShift](http://openshift.com/)

This example uses: Meteor example application (leaderboard) with custom version of nodeJS ready for deploy to openshift.


<a href='https://openshift.redhat.com/community/blogs/cloudy-with-a-chance-of-meteorjs'><img width="300" height="200" src='https://www.meteor.com/universe.png'/></a>

## Configure your OpenShift gear 
 We use any old nodeJS (node 0.6), but we need some "cartridge", finally it uses nodeJs version 10.24 (but you can change it - se below).  Name of our application is "myapp". 

 There are 2 choices: one gear (1 Gear = NodeJs+Mongo) or scaled. I recomended you use scalable version, because Mongo with Node is to big for one gear.
 Not-scalable solution (MongoDb has 0.5Gb, Suma is 0.8Gb, Limit is 1Gb !!): 
     
     rhc app create myapp  nodejs-0.6 mongodb-2.2  --from-code=git://github.com/vladka/openshift-meteor-leaderboard-customNode.git
     #see quota:
     rhc show-app myapp --gears quota

 Scalable solution(Mongo has own gear, it is great!):

     rhc app create myapp  nodejs-0.6 --from-code=git://github.com/vladka/openshift-meteor-leaderboard-customNode.git -s     
     rhc cartridge add mongodb-2.2 -a myapp
     #You can limit scale-number (optionally):
     rhc scale-cartridge nodejs-0.6 -a myapp --min 1 --max 2

     #Now push something to rebuild all.

The above command will output a local copy of your OpenShift application source in a folder matching your application name.  Be sure to run this command from within a folder where you would like to keep your project source.

That's it! Check out your new Meteor.js application at:

    http://myapp-$yournamespace.rhcloud.com

ENJOY ! Vladka.

## Change version of nodeJS
 Example above uses node version 0.10.24. 
 You can change it anywhere by changing file 
 on the path: 

    myapp/.openshift/markers/NODEJS_VERSION 

then don't forgot to push changes to openshift 
   
    git add . 
    git commit -am "any message"
    git push

## Switch from 'Demo app (leaderboard) to your real application'

    In my case :
    1) I have used 'demeteorizer' (see article http://blog.modulus.io/demeteorizer)
    2) then I have copied 'demeteorized' content to myapp, except file package.json.
    3) Package.json file I have manually "merged" (there are dependencies, which you must keep from your application)
    4) git add, commit, push
    5) see your output: 
    
    rhc tail myapp

    6) if you see errors, if some dependency is missing: 
      add it to 'Package.json' and go to step 4)

This solution worked for me.


### For beginers : OpenShift Online Setup 
In this quickstart guide, we'll be using OpenShift Online to host our application.

Sign up for an account at http://openshift.redhat.com/app/account/new

If you don't already have the `rhc` [(Red Hat Cloud) command-line tools](https://openshift.redhat.com/community/get-started#cli), install them:

    sudo gem install rhc

You'll need to run `rhc setup` to link your OpenShift Online account with your local development environment, and to select an application namespace:

    rhc setup

If you need any additional assistance setting up `rhc`, this doc may come in handy: https://openshift.redhat.com/community/developers/rhc-client-tools-install
