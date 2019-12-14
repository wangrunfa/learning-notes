# java封装jar包导入

mvn install:install-file -Dfile=C:\Users\Administrator\Desktop\githubBusiness\mavendemo\target\mavendemo-0.0.1-SNAPSHOT.jar -DgroupId=com.UUID -DartifactId=UUIDUtil -Dversion=1.0.0 -Dpackaging=jar



install-file -Dfile：路径

DgroupId  文件夹名

DartifactId  文件名

Dversion  版本号

Dpackaging  类型