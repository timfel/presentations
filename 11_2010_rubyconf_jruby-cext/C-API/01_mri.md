!SLIDE
## Internal API in MRI

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

