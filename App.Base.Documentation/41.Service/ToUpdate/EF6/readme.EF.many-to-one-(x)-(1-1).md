# About #

An example would be any record that had a optional Category.

## Key Design Points ##

* (Body) MUST to have an FK of the referenced record (Category).
* Because (Category) is defined as Required, the FK must not be Nullable (ie, Guid)
  * When Category is deleted, the FK cannot be set to Null.
  * So Body will be deleted by cascade.
  * Setting `.WillCascadeOnDelete(false)` cannot be applied in this case.
* (Invoice) MAY have a Entity Navigation Property to (Category). 
* (Category) MAY nave a Collection Navigation Property back to (Body), but that is not common.
  * Hence `WithMany()` has no arguments.

## Example ##

The following does not have a Navigation Collection Property on (Category)
because .WithMany() has no arguments:


    modelBuilder.Entity<Body>()
        .HasRequired(x => x.Category)
        .WithMany()
        .HasForeignKey(x => x.CategoryFK);


    public class Body 
    {
		...
        public virtual Guid CategoryFK { get; set; }
        public virtual BodyCategory Category { get; set; }
		...
	}
    public class BodyCategory 
    {
		...
		public virtual Guid Id {get;set;}
		...
    }
