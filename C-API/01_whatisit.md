!SLIDE
## C API … What's that all about?

** The Ruby C API used by extensions relies on implementation details of the Ruby 1.8 VM **
<p align="right">(That's why there's no full 1.9 support, either)</p>  

	@@@ c
	/* call-seq:
	*     ary.each {|item| block }   -> ary
	*     ary.each                   -> an_enumerator
	*/
	VALUE 
	rb_ary_each(VALUE ary)
	{
		long i;
		
		RETURN_ENUMERATOR(ary, 0, 0);
		for (i=0; i<RARRAY_LEN(ary); i++) {
			rb_yield(RARRAY_PTR(ary)[i]);
		}
		return ary;
	}

!SLIDE smbullets
## C API is holding Ruby back! ##

* Direct pointer access
	1. No way to change GC semantics
	2. No support for multiple runtimes in one VM
* Ruby threads exposed
	1. Cannot to switch to native threads
* Ruby data structures laid bare
	1. Changing the structure requires emulating old behaviour

