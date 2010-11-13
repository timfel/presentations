!SLIDE
## Internal API in JRuby

	@@@ java
	
	/** rb_ary_push_m - instance method push
	 *
	 */
	@JRubyMethod(name = "push", rest = true, compat = CompatVersion.RUBY1_8)
	public RubyArray push_m(IRubyObject[] items) {
	    for (int i = 0; i < items.length; i++) {
	        append(items[i]);
	    }
	    
	    return this;
	}

