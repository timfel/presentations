!SLIDE smbullets incremental
## “But there's a fast C library already out there!”
* Encryption and authentication?
* Image processing?
* What about forking, system-calls …

!SLIDE
# FFI To The Rescue!
	@@@ ruby
	module SimpleKrb5
		extend FFI::Library
		
		ffi_lib File.expand_path("../ext/krb5_simple_auth.so", __FILE__)
		attach_function "Krb5_get_init_creds_passwd", [:string, :string], :int
		
		def self.authenticate(user, password)
			Krb5_get_init_creds_passwd(user, password) == 1
		end
	end
	
!SLIDE
# And if that isn't enough...