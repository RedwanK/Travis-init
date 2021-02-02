## Setup Travis CI
---

Travis CI uses .yml files (like most CI services eg : Bitbucket Pipeline, GitLab CI but not Jenkins that uses Groovy files).

mvn clean verify + npm run test = clear previous builds cache an drun test on fresh builds.

Unit test : Test the code functionnality
Component test : test the project functionnality (interactions between differnt functionnalities)

testcontainers : JAVA librairies that simplifies the use of Docker containers for testing.

two stages : one for java and anotyher for node.js, for the java one Travis detects maven through the pom.xml file.

###### Checkpoint CI :

1. Create GitHub repo
2. Link GitHub to Travis and import repo
2. Add working .travis.yml
3. Commit and push
4. Sit back and enjoy

## First steps into CD world
---

Branch develop : we don't want to build a docker image each time we push and we don't want to push all our modifications on main branch

Secured variables : for security I guess ... Those are our looging credentials.

Publish docker images, for what purpose : Everytime you push a functionnal version your colleagues will be able to pull these images to test on their machine + you always have a "latest" version of your project.

###### Checkpoint CD : quality gate :

1. Import repo on SonarCloud
2. Add correct lines to .travis.yml
3. Commit and push
4. Sit back and ... Go work on your sh***y code

###### Checkpoint CD : splitted pipeline :

When writing your .travis.yml :

*A new stage for each job you want to give to Travis*
(or just common sense, don't restart the whole process for each new job ...)
