# About #

A one to many is when a record has a of collection of zero or more other records.

The other records can be child records, that will be deleted when the parent record is deleted,
or simply other records, that will continue to have a lifespan after the parent record is deleted.

So there are different One-to-Many scenarios:

* where the parent (principal) record owns a Collection of Child (dependent) records, 
  deleted when the Parent is deleted. 
  Examples include Person and PersonDetails, Invoice and LineItems, etc.
  The LineItems are deleted because the FK is Required, and WillCascadeOnDelete is true. 
* when a principal record has a collection of (dependent) records that it will not delete when 
  then the parent (principal) record is deleted.
  Examples include Person and Offices (when you delete the Person, you don't delete the Offices,
  you just NULL the FK to the Person).
  The records are left alone, because WillCascadeOnDelete is set to false, and Null is allowed
  because the FK is made an Optional Guid.
* when a principal record has a collection of (dependent) records that it will not delete when 
  then the parent (principal) record is deleted.
  Examples include Person and Offices (when you delete the Person, you don't delete the Offices,
  but you Require that the FK is not left Null, but replaced with another Person's Id).
  The records are left alone, because WillCascadeOnDelete is set to false, and Null is not allowed
  because the FK is made an Required Guid.
* When a record has a collection of the same type of Records (ie, recursive). 
  

Note: 
there's common misconception of what 'Optional' and 'Required' have to do with Collections. 
Whereas one can define a single record as Required or Optional (see many to optional one (x)-(0-1)
or one to required one (x)-(1-1)) you can't define a collection as having to have at least one dependent
record in the collection. That's not what the Required/Optional statement is for. It's for defining whether
the Dependent's FK needs to have a value, and what to allow or not.



## Key Design Points ##

* (Body) MUST have a Collection Navigation Property of (BodyProperty)s.
* (BodyProperty) MUST have an FK back to (Body)
* (BodyProperty) MAY have a Entity Navigation Property back to (Body) but that is uncommon.
  * Hence `.WithOptional()` has no arguments.
* When we Delete (Body) we want it delete its children so we leave in place the default cascade behaviour. 


modelBuilder.Entity<Course>() 
    .HasRequired(t => t.Department) 
    .WithMany(t => t.Courses) 
    .HasForeignKey(d => d.DepartmentID) 
    .WillCascadeOnDelete(false);

## Example ##


	modelBuilder.Entity<Body>()
		.HasMany(x => x.Properties)
		.WithOptional()
		.HasForeignKey(x => x.OwnerFK)
		WillCascadeOnDelete(true);

    public class Body 
    {
		...
        public virtual ICollection<BodyProperty> Properties
        {
            get
            {
                if (this._properties == null)
                {
                    this._properties = new Collection<BodyProperty>();
                }
                return this._properties;
            }
            set => this._properties = value;
        }
        private ICollection<BodyProperty> _properties;
		...
	}
    public class BodyProperty
    {
		public virtual Guid Id {get;set;}
        public virtual Guid OwnerFK { get; set; }
		...
    }
