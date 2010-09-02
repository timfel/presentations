!SLIDE
# Optimize critical code paths
	@@@ ruby
		# 9e72b25 (rkh) - avoid creating procs, 10% speedup (7 days ago)
		def each
			yield self
	-		child.try(:each, &block)
	+		child.try(:each) { |e| yield e }

<p class="right_and_up">
	<img src="engineering.png"/>
</p>