!SLIDE
# There's different parts to the C API

!SLIDE
# Ruby code in C

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

!SLIDE
# This is easy to do…

	@@@ c
	extern "C" VALUE
	rb_ary_push(VALUE array, VALUE val)
	{
		return callMethod(array, "push", 1, val);
	}

---
	@@@ java
	public class JRuby {
	   public static long callRubyMethod(IRubyObject recv, Object methodName, IRubyObject[] args) {
	       IRubyObject retval = recv.callMethod(recv.getRuntime().getCurrentContext(),
	               methodName.toString(), args);
	
	       return Handle.nativeHandle(retval);
	   }
	…

## Not fast, but safe.

!SLIDE
# On to more scary stuff

	@@@ ruby
	>> str = "42"; c_mutate_string(str); puts str
	==> 23

---

	@@@ c
	VALUE c_mutate_string(VALUE ruby_string) {
		char* c_string = RSTRING(ruby_string)->ptr;
	}
	c_string[0] = '2';
	c_string[1] = '3';

!SLIDE
# Synchronization of Structures in JRuby
	@@@ c
	struct RString {
	    struct RBasic basic;
	    long len;
	    char *ptr;
	    long capa;
	};
	#define RSTRING(str) jruby_rstring((str))

!SLIDE

	@@@ c
	extern "C" struct RString*
	jruby_rstring(VALUE v)
	{
	    return jruby_str(v)->toRString(false);
	}

!SLIDE smaller
	@@@ c
	RString* RubyString::toRString(bool readonly)
	{
			…
	    JLocalEnv env;
	    rwdata.jsync.data = this;
	    rwdata.jsync.sync = RubyString_jsync;
	    rwdata.nsync.data = this;
	    rwdata.nsync.sync = RubyString_nsync;
	    rwdata.clean.data = this;
	    rwdata.clean.sync = RubyString_clean;
	    rwdata.rstring = (RString *) j2p(env->CallStaticLongMethod(JRuby_class, JRuby_getRString, obj));
	    checkExceptions(env);
	    rwdata.readonly = readonly;
	
	    TAILQ_INSERT_TAIL(&jruby::cleanq, &rwdata.clean, syncq);
	    TAILQ_INSERT_TAIL(&jruby::jsyncq, &rwdata.jsync, syncq);
	    if (!readonly)
	        TAILQ_INSERT_TAIL(&jruby::nsyncq, &rwdata.nsync, syncq);
	    nsync(env);
	
	    return rwdata.rstring;
	}

!SLIDE center
### If we need more, the situation is dire indeed.
![dire](dire.png)

!SLIDE smbullets
# Things like
* `rb_iterate`
* `NODE(x)`
* `rb_thread_blocking_region`
* `rb_io_fd`
* …

<div style="font-size: 2em">
Either don't work or work unsafely.  
</div><br/>
<div style="font-size: 1.2em">
Try those methods on JRuby and they might eat your JVM, wipe your hard-drive,
hunt down your children and drink their blood.
<div>