# About #

So it's appropriate to provide a Many-To-Many relationship transparently by relying on a database Junction Table.

That is until you need to add a Property, or Behavior -- in which case you will need more than a Junction Table.


## Key Design Points ##

* Whereas a simple Junction Table (defined using the .Map() method) does not produce any Object, 
  this approach does. The benefit is that one can attach properties to the Object.
* An example of the difference is as follows:
  * Course -> Teachers[] can be done using a tranparent Map, whereas
  * Course -> Teacher(by Role) -> Teacher allows for defining *how* the Teacher is assigned to the Course.
  
* Whereas in a simple Many-To-Many scenario the name of the Junction Tbale and its attributes 
  are defined using the .Map() method on the (Principal) table, it's more straight forward in this case: 
  you just define the table name and column names when you define the Junction Object (CourseEnrollment),
  as you would do when you define any object.


## Implementation Example ##

As a quick recap, a simple Junction Table is created as follows:

            modelBuilder.Entity<Principal>()
                .HasMany(s => s.Roles)
                .WithMany();

But notice that the Collection Navigation Property is for the target entity (Roles) directly.

A Junction Object is first developed in its own rights, taking specific care to make the Key
a composite Key:

    // Create Composite Key:
    // See: https://stackoverflow.com/a/9960987
    modelBuilder.Entity<CourseInstructorRole>()
        .HasKey(x => new { x.CourseFK, x.ResourceFK });

    modelBuilder.Entity<CourseInstructorRole>()
        .Property(x => x.CourseFK)
        .HasColumnOrder(order++)
        .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None)
        .IsRequired();

    modelBuilder.Entity<CourseInstructorRole>()
        .Property(x => x.ResourceFK)
        .HasColumnOrder(order++)
        .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None)
        .IsRequired();



Once the Junction table is developed, you have two ways to go forward.

If the Junction table is to be navigated in both directions, you may feel it
easier to keep track of properties when they are developed on the two end
tables:


       modelBuilder.Entity<CourseEnrollee>()
            .HasMany(x => x.Enrollments)
            .WithRequired(x => x.Student)
            .HasForeignKey(x => x.StudentFK)
			// When you delete this object you don't want to delete the foreign objects
            .WillCascadeOnDelete(false);
			;

and:
            // See: https://stackoverflow.com/a/9960987
        modelBuilder.Entity<Course>()
            .HasMany(x => x.Enrollments)
            .WithRequired(x => x.Course)
            .HasForeignKey(x => x.CourseFK)
			// When you delete this object you don't want to delete the foreign objects
            .WillCascadeOnDelete(false);
			;

But if the table is to be navigated only one way (so there is a collection on only one object)
then consider to define the relationship on the join object as follows:

           // --------------------------------------------------
            // Entity Navigtation Properties:
            modelBuilder.Entity<RolePermissionAssignment>()
             .HasRequired(x => x.Role)
             .WithMany(x => x.PermissionsAssignments)
             .HasForeignKey(x => x.RoleFK)
             ;

            modelBuilder.Entity<RolePermissionAssignment>()
             .HasRequired(x => x.Permission)
             // Notice how on this side we are not specifying any collection:
			 .WithMany()
             .HasForeignKey(x => x.PermissionFK)
             ;



### TroubleShooting ###

The `.WillCascadeOnDelete(false);` is needed or 
else when you delete the Junction Table entry would have caused deletion of the 
two primary tables (Course) and (CourseEnrollee).

COnsider the following:

Introducing FOREIGN KEY constraint 'FK_Module1.CourseEnrollments_Module1.Courses_CourseFK' on table 'CourseEnrollments' may cause cycles or multiple cascade paths. 
Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.



## Resources ##
* https://stackoverflow.com/a/9960987