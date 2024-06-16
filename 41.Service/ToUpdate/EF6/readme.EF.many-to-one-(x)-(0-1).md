# About #

An example would be any record that had a optional Category.

## Key Design Points ##

* (Body) MUST to have an FK of the referenced record (Location).
* Because (Location) is defined as Optional, the FK must be Nullable (ie, Guid?)
* Because (Location) is Optional, when a record is deleted, the FK should be set to Null.
  * So use `WillCascadeOnDelete(false)`
* (Body) MAY have a Navigation Property to (Location). 
* (Location) MAY nave a Collection Navigation Property back to (Body), but that is not common.

## Example ##

The following does not have a Navigation Collection Property on (Category)
because .WithMany() has no arguments:


            modelBuilder.Entity<Body>()
                .HasOptional(i => i.Location)
                .WithMany()
                .HasForeignKey(x => x.LocationFK)
                .WillCascadeOnDelete(false) //so that if you delete the Child, the user is not lost.



    public class Body 
    {
		...
		public virtual Guid? LocationFK { get; set; }
		public virtual BodyLocation Location { get; set; }
		...
	}
    public class BodyLocation 
    {
		...
		public virtual Guid Id {get;set;}
		...
    }


