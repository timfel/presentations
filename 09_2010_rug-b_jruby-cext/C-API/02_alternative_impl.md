!SLIDE
## API in JRuby follows C API

	@@@ java
	/** rb_ary_each */
	public IRubyObject eachCommon(ThreadContext context, Block block) {
		if (!block.isGiven()) {
			throw context.getRuntime().newLocalJumpErrorNoBlock();
		}
		for (int i = 0; i < realLength; i++) {
			block.yield(context, values[begin + i]);
		}
		return this;
	}

### But that's not the same
