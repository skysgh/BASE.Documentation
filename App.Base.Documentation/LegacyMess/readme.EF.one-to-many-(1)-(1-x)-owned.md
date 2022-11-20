# About #

## Key Design Points ##

* (Body) MUST have a Collection Navigation Alias of (BodyAlias)s.
* (BodyAlias) MUST have an FK back to (Body)
* (BodyAlias) MAY have a Entity Navigation Alias back to (Body) but that is uncommon.
  * Hence `.WithOptional()` has no arguments.
* When we Delete (Body) we want it delete its children so we leave in place the default cascade behaviour. 
  * CHECK: because we can't configure Cascade delete on Collection Navigation Properties, if we wanted to 
    we would have to flip it around to be a many to many definition, and work on the OwnerFK definition.
	
## Example ##

THe syntax is crappy, but '.WithRequired' makes the collection 1-*.

	modelBuilder.Entity<Body>()
		.HasMany(x => x.Aliases)
		.WithRequired()
		.HasForeignKey(x => x.OwnerFK);

    public class Body 
    {
		...
        public virtual ICollection<BodyAlias> Properties
        {
            get
            {
                if (this._aliases == null)
                {
                    this._aliases = new Collection<BodyAlias>();
                }
                return this._aliases;
            }
            set => this._aliases = value;
        }
        private ICollection<BodyAlias> _aliases;
		...
	}
    public class BodyAlias
    {
		public virtual Guid Id {get;set;}
        public virtual Guid OwnerFK { get; set; }
		...
    }
