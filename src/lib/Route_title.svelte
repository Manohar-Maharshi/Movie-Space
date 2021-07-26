<h1>
	<slot></slot>
</h1>



<style>
	h1{
		user-select: none;
		overflow: hidden;
	    text-align: center;
	    font-size: 2rem;
	    margin: 0.9rem 0 0.5rem 0;
	    padding:0.3rem 0;
	    color: #fff;
	}
	h1::before,h1::after{
		background-color: #C84B31;
		content: "";
	    display: inline-block;
	    height: 3px;
	    position: relative;
	    vertical-align: middle;
	    width: 10%;
	}
	h1::before{
		right: .5em;
    	margin-left: -50%;
	}
	h1::after{
		left: .5em;
    	margin-right: -50%;
	}
</style>