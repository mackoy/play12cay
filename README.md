# Play 1.2 Cayenne Module

The Play 1.2 Cayenne module allows you to use the Apache Cayenne ORM with your Play application. It provides easy integration of Apache Cayenne models into Play. It has a data binder for Cayenne objects, configuration of Cayenne via conf/application.conf. Every model class is enhanced to have a `findById()` static method. Cayenne validation errors are available in Play validation object. Custom tag `play12cay.select` which renders to form elements to easily modify a to-one or to-many relationship of a Cayenne object.


# Getting started

## Conventions

All relative paths are meant to be relative to the play application root.


## Play app setup

1.  Create play app with `play new <name of app>`
2.  Edit `conf/dependencies.yml` to require play12cay -> play12cay e.g.:
<pre>
> require:
>     - play
>     - play12cay -> play12cay 0.3
</pre>
3. Configure database in `conf/application.conf` e.g.:
<pre>
db.driver=org.postgresql.Driver
db.url=jdbc:postgresql://localhost:5432/example-db
db.user=example-user
db.pass=secret
</pre>


## Cayenne Model

1. Create new model with CayenneModeler
2. Save it to `conf/cayenne` directory
3. In CayenneModeler -> DataMap -> Entity Defaults:
   * set 'Java Package' to 'models' and click 'Update'-Button
   * set 'Custom Superclass' to 'PlayDataObject' and click 'Update'-Button
4. Generate Classes with CayenneModeler
   1. CayenneModeler -> Tools -> Generate Classes
   2. set 'Output Directory' to 'app'-directory of your play app
   3. set 'Superclass Package' to models.auto
   4. in 'Classes' tab make sure all classes are checked
   5. click 'Generate'-Button


## Play Controllers

Make every Controller inherit from `CayController`. `CayController` has a static method `saveChanges()` which you should call on when you want to save changes to your Cayenne objects. This method returns `false` if any validation error occured. Validation errors are available through the play `validation` object. Every `CayController` also provides a method named `oc()` which returns the current Cayenne-`ObjectContext` which can be used to query Cayenne objects from the database. It should not be used to commit changes, use `CayController.saveChanges()` instead.

### Example Controller

```java
public class Companies extends CayController {

    public static void index() {
        List<Company> companies = oc().performQuery(new SelectQuery(Company.class));
		
        render(companies);
    }
	
    public static void create() {
        Company company = oc().newObject(Company.class);

        _edit(company);
    }
    
    public static void edit(Integer companyId) {
        Company company = Company.findById(companyId);
        
        _edit(company);
    }
    
    private static void _edit(Company company) {        
        List<Person> people = oc().performQuery(new SelectQuery(Person.class));
        
        renderTemplate("Companies/edit.html", company, people);
    }
    
    public static void delete(Integer companyId) {
        Company company = Company.findById(companyId);
        
        oc().deleteObject(company);
        saveChanges();
        
        index();
    }
    
    public static void update(Company company) {
        if(!saveChanges()) {
            _edit(company);
        } else {
            index();
        }
    }
}
```