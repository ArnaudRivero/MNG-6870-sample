# MNG-6870

JIRA: [MNG-6870](https://issues.apache.org/jira/projects/MNG/issues/MNG-6870)

## Description
This issue occurs when a pom.xml is using variable interpolation in its private repository definition and has its parent
pom.xml in that repository. It seems that Maven is using the repository definition as-is, instead of replacing the 
variable with its value. 

### Steps to reproduce
1) Run **mvn deploy** in the **parent-pom** module. It should create a folder *repository* containing its *pom.xml* 
2) Remove your *com.bosion.maven:parent-pom:1.0.0-SNAPSHOT* artifact from your local *.m2/repository* cache in order 
to make it look like it is missing and needs to be downloaded
3) Run **mvn validate** in the **child-interpolating-pom** module. It should fail, because it uses interpolation in 
order to get the URI pointing to the repository
4) Run **mvn validate** in the **child-non-interpolating-pom** module. It should work, because it doesn't use it
5) Before trying to re-run **mvn validate** in **child-interpolating-pom**, make sure to remove the artifact 
*com.bosion.maven:parent-pom:1.0.0-SNAPSHOT* from your local *.m2/repository* cache. Otherwise, Maven will find it and 
won't try to download it from the remote repository, thus ignoring the *repositories* tag

In that project, we are using a local repository, but it works just the same with a remote one

## Update
Variable interpolation in repositories is done after the parent resolution, but before the dependencies resolution, 
making it just as unreliable

### Steps to reproduce
1) Run **mvn deploy** in the **dependency-pom** module
2) Remove the artifact *com.bosion.maven:dependency-pom:1.0.0-SNAPSHOT* from your *.m2/repository* cache
3) Run **mvn compile** in the module **dep-interpolating-pom**. It should be working because in that case, interpolation 
is indeed done