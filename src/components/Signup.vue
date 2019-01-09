<template>
	<div class="email-subscribe">
		<section class="email-sec">
				<form id="email-signup">
					<p>Signup to be notified about latest beats</p>
					<input type="text" placeholder="Enter your email...">
					<div class="input-append">
						<button>SUBSCRIBE</button>
					</div>
				</form>
		</section>
	</div>
</template>
<script>
	export default{
		name: 'signup',
	};
</script>
<style>
	.email-subscribe{
  		display: -moz-box;
  		display: -webkit-flex;
  		display: -ms-flexbox;
		display: flex;
		justify-content: center;
	}
	.email-sec{
		display: inline-block;
    	position: relative;
    	margin-top: 9px;
	}
	#email-signup{
		display: flex;
	}
	#email-signup > p{
		margin: auto;
	}
	#email-signup > input{
		margin-left: 16px;
	    height: 45px;
	    padding: 10px 16px;
	    font-size: 18px;
	    line-height: 1.33;
	    width: 290px;
	}
	.input-append{
		position: absolute;
	    top: 8px;
	    right: 6px;
	}
	.input-append > button{
		background: #94b8b8;
		color: white;
	    border: 1px;
	    border-radius: 5px;
	    font-family: initial;
	    width: 105px;
    	height: 28px;
	}
	input:focus{
		border: 1px solid skyblue;
	}
	@media (max-device-width:500px){
		#email-signup{
			display: flex;
			flex-direction: column;
		}
		#email-signup > input{
		    font-size: 12px;
		    line-height: 1.33;
		    width: 290px;
		}
		.input-append{
		    top: 29px;
		    right: 6px;
		}
	}
</style>