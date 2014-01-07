# Play 1.2 Cayenne Module

The Play 1.2 Cayenne module allows you to use the Apache Cayenne ORM with your Play application. It provides easy integration of Apache Cayenne models into Play. It has a data binder for Cayenne objects, configuration of Cayenne via conf/application.conf. Every model class is enhanced to have a findById() static method. Cayenne validation errors are available in Play validation object. Custom tag `play12cay.select` which renders to form elements to easily modify a to-one or to-many relationship of a Cayenne object.

