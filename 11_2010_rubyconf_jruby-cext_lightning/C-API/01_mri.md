!SLIDE
# Down the Rabbit Hole

	@@@ c
	/*
	 *  call-seq:
	 *     ary << obj            -> ary
	 *
	 *  Append---Pushes the given object on to the end of this array. This
	 *  expression returns the array itself, so several appends
	 *  may be chained together.
	 *
	 *     [ 1, 2 ] << "c" << "d" << [ 3, 4 ]
	 *             #=>  [ 1, 2, "c", "d", [ 3, 4 ] ]
	 *
	 */
	VALUE rb_ary_push(VALUE ary, VALUE item)


