# About #

## Key Design Points ##

* (User) has an Entity Navigation Property to (BillingAddress)
* (BillingAddress) has an FK 
* CHECK: The Ids will be shared across both Models.


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

