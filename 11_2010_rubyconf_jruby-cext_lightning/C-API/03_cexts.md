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
# More scary stuff

	@@@ ruby
	>> str = "42"; c_mutate_string(str); puts str
	==> 23

---

	@@@ c
	VALUE c_mutate_string(VALUE ruby_string) {
		char* c_string = RSTRING(ruby_string)->ptr;
		c_string[0] = '2';
		c_string[1] = '3';
		return Qnil;
	}

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