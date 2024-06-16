# About # 
The following is a crash course of the most common relationships between entities.

(Note, admittedly this was written for EF6, so some extrapolation to current approaches is required)

## Examples ##


### Many to One:

The following is an example of relationship between an Entity and Reference Data (eg: Categories).
Note that the Parent object has both an FK (eg: x.CategoryFK) to the Reference Data (eg: Categories), 
and defines the navigation property (x.Category):

If the Data is Required, then it's simple:
            modelBuilder.Entity<Body>()
                .HasRequired(x => x.Category)
                .WithMany()
                .HasForeignKey(x => x.CategoryFK);

As it is required, if you delete the reference data (eg: CategoryA), then you expect to delete all the Referencing records (ie, Body)
or else you would have referential integrity issues.


But if the Reference Data is Optional, then you have another option. When you delete the Reference Data entry (eg: CategoryA)
do you want to delete all the Body records or not? If you want them to remain, but be set to NULL, then you need both the 'Optional'
statement AND the WillCascadeOnDelete(false)


            modelBuilder.Entity<Body>()
                .HasOptional(i => i.Location)
                .WithMany()
                .HasForeignKey(x => x.LocationFK)
                .WillCascadeOnDelete(false) //so that if you delete the Child, the user is not lost.
                ;



### One to Many

If you have a a Parent object has a Collection of child records (eg: Properties), 
then the child object has to have an FK pointing back to the parent (x.OwnerFK, within Properties).

If the collection can be 0-* then use WithOptional:

            modelBuilder.Entity<Body>()
                .HasMany(x => x.Properties)
                .WithOptional()
                .HasForeignKey(x => x.OwnerFK);

If the list must be 1-* then Use WithRequired()



### Many To Many

Probably the most difficult to grasp is Many to Many, but it's well worth getting the hang of it.

A simple exampe, where the Join table is created based on convention would be:

modelBuilder.Entity<Course>() 
    .HasMany(t => t.Instructors) 
    .WithMany(t => t.Courses)

But if you want to control the shape of the Join table, you need the Map() command.


The following defines a parent object that has an optional set of Tags:

        public void Define(DbModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Principal>()
                .HasMany(p => p.Tags)
                .WithMany()
                .Map(x =>
                {
                    x.MapLeftKey("PrincipalFK");
                    x.MapRightKey("TagFK");
                    x.ToTable("Principal2Tag");
                });
        }

THe same applies, whether we are talking about Tags or Roles:


            modelBuilder.Entity<Principal>()
                .HasMany(s => s.Roles)
                .WithMany()
                .Map(cs =>
                {
                    cs.MapLeftKey("PrincipalFK");
                    cs.MapRightKey("RoleFK");
                    cs.ToTable("Principal2Role");
                });
        }


If the association will lead to a too unwieldy collection on the parent object (eg: a Subscription, with thousands of members...) then use .HasMany(), without defining a Collection Property
(the link will be maintained logically, but without a navigation property). You will have to query for members manually...although (TODO) Not sure how to do that yet.

Better yet, consider flipping it around, as the collection will be smaller when you think of a Principal being in only a small set of Subscriptions.

Another example of many to many:

    modelBuilder.Entity<Student>()
                .HasMany<Course>(s => s.Courses)
                .WithMany(c => c.Students)
                .Map(cs =>
                        {
                            cs.MapLeftKey("StudentRefId");
                            cs.MapRightKey("CourseRefId");
                            cs.ToTable("StudentCourse");
                        });

on objects:

Student {
  ...
  ICollection<Course> Courses {get;set;}
}
and
Course 
{
...
  ICollection<Student> Students {get;set;}
...

}



### A One to One

Sometimes a record can be split across several records - as child records that are not automatically loaded as they not often needed.

/1-1:
       modelBuilder.Entity<User>()
           .HasRequired(i => i.BillingAddress)
           .WithMany(); //1-0: as it can be shared with User, don't bind Invoice and Address, so avoid .WithRequired


//////1-0..1:
     modelBuilder.Entity<Invoice>()
         .HasRequired(i => i.ShippingAddress)
         .WithMany()
         .WillCascadeOnDelete(false); //1-0: as it can be shared with User, don't bind Invoice and Address, so avoid .WithRequired

