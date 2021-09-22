<template>
	
	<div class="login-box">
  		
  		<div class="login-logo">
	 		
	 		<a><b>Dynamic</b> FORM</a>
  		</div>

  		<div class="card">
	 		
	 		<div class="card-body login-card-body">
				
				<p class="login-box-msg" v-if="!showSubjectForm && !showTable">Enter Details to Generate Form</p>

				<template  v-if="!showSubjectForm && !showTable">

					<div>

				  		<div class="form-group">
		         
		              	<label for="workingdays">No of Working days</label>
		              
		              	<input name="workingdays" v-model="workingdays" v-validate="'required|numeric|between:1,7'" :class="{'input': true, 'is-danger': errors.has('workingdays') }" type="text" placeholder="Enter No of Working days" class="form-control">

	                	<i v-show="errors.has('workingdays')" class="fas fa-warning"></i>

	                	<span v-show="errors.has('workingdays')" class="help is-danger">{{ errors.first('workingdays') }}</span>
		            </div>

		            <div class="form-group">
		         
		              <label for="subjectsperday">No of Subjects per day</label>
		         
		              	<input name="subjectsperday" v-model="subjectsperday" v-validate="'required|numeric|between:1,8'" :class="{'input': true, 'is-danger': errors.has('subjectsperday') }" type="text" placeholder="Enter No of Subjects per day" class="form-control">

	                	<i v-show="errors.has('subjectsperday')" class="fas fa-warning"></i>

	                	<span v-show="errors.has('subjectsperday')" class="help is-danger">{{ errors.first('subjectsperday') }}</span>
		            </div>

		            <div class="form-group">
		         
		              <label for="totalsubjects">Total Subjects</label>
		          
		              <input name="totalsubjects" v-model="totalsubjects" v-validate="'required|numeric|min_value:1'" :class="{'input': true, 'is-danger': errors.has('totalsubjects') }" type="text" placeholder="Enter Total Subjects" class="form-control">

	                	<i v-show="errors.has('totalsubjects')" class="fas fa-warning"></i>

	                	<span v-show="errors.has('totalsubjects')" class="help is-danger">{{ errors.first('totalsubjects') }}</span>
		            </div>
		         </div>

					<div class="social-auth-links text-center mb-3">

			  			<a href="javascript:;" class="btn btn-block btn-primary" @click="onClick">
				 			
				 			<i class="far fa-clock mr-2"></i> Generate total hours of week
			  			</a>
					</div>
				</template>

				<template  v-if="showSubjectForm && !showTable">

					<div class="alert alert-danger alert-dismissible mb-2" v-if="showAlert">
               
                  <button type="button" class="close" data-dismiss="alert" aria-hidden="true">×</button>
                  The total hours of subject must be equal to Total hours for week.
               </div>
					
					<p class="lead">Total hours for week : <b>{{totalHours}}</b></p>

					<div>
						
						<div class="row mb-2" v-for="(field,index) in subArray">
                  	
                  	<div class="col-6">
                    		
                    		<input :name="'subject'+index" v-model="field.subject" v-validate="'required'" :class="{'input': true, 'is-danger': errors.has('subject'+index) }" type="text" placeholder="Enter Subject" class="form-control">

		                	<i v-show="errors.has('subject'+index)" class="fas fa-warning"></i>

		                	<span v-show="errors.has('subject'+index)" class="help is-danger">{{ errors.first('subject'+index) }}</span>
                  	</div>
                  	
                  	<div class="col-6">
                    	
                    		<input :name="'hour'+index" v-model="field.hour" v-validate="'required|numeric|min_value:0'" :class="{'input': true, 'is-danger': errors.has('hour'+index) }" type="text" placeholder="Enter Hours" class="form-control">

		                	<i v-show="errors.has('hour'+index)" class="fas fa-warning"></i>

		                	<span v-show="errors.has('hour'+index)" class="help is-danger">{{ errors.first('hour'+index) }}</span>
                  	</div>
                	</div>
					</div>

					<div class="social-auth-links text-center mb-3">

			  			<a href="javascript:;" class="btn btn-block btn-primary" @click="onSave">
				 			
				 			<i class="far fa-list-ul mr-2"></i> Generate Table
			  			</a>
					</div>
				</template>

				<template v-if="showTable">
					
					<table class="table table-bordered">
                  
                  <tbody>

                    <tr v-for="field in tableArr">
                      
                      <td v-for="data in field">{{data}}</td>
                      
                    </tr>
                  </tbody>
                </table>
				</template>
	 		</div>
  		</div>
	</div>
</template>

<script>

	export default {

		name : 'dynamic-form',

		data() {

			return {

				workingdays : '',

				subjectsperday : '',

				totalsubjects : '',

				showSubjectForm : false,

				showAlert : false,

				showTable : false,

				subArray : [],

				tableArr : [],
			}
		},

		beforeMount() {

			this.errors.clear();
		},

		computed : {

			totalHours() {

				return this.workingdays * this.subjectsperday
			}
		},

		methods : {

			onClick() {

				this.showAlert = false;

				this.$validator.validateAll().then((result) => {
		        
		        	if (result) {

		        		this.errors.clear();

		        		for(let i = 0; i < this.totalsubjects;i++){

		        			this.subArray.push({ subject : '', hour : ''})
		        		}

		          	this.showSubjectForm = true;
		          	
		          	return;
		        	}
		      });
			},

			onSave() {

				this.showAlert = false;

				this.$validator.validateAll().then((result) => {
		        
		        	if (result) {

		        		this.errors.clear();

		        		let verifyHours = this.subArray.reduce(function(acc,curr){

		        			acc = acc + parseInt(curr.hour)

		        			return acc
		        		},0);

		        		if(this.totalHours === verifyHours) {

		        			this.showAlert = false;

		        			this.showTable = true;

		        			this.showSubjectForm = false;
		        			
		        			let filledArray = [];

		        			for(var i in this.subArray) {

		        				filledArray.push(new Array(parseInt(this.subArray[i].hour)).fill(this.subArray[i].subject));
		        			}

		        			filledArray = [].concat.apply([], filledArray);

		        			for (let i = filledArray.length - 1; i > 0; i--) {

						        const j = Math.floor(Math.random() * (i + 1));
						      
						        [filledArray[i], filledArray[j]] = [filledArray[j], filledArray[i]];
						   }

						   this.tableArr = this.chunkArray(filledArray, parseInt(this.workingdays));

		        		} else {

		        			this.showAlert = true;
		        		}
		        
		          	return;
		        	}
		      });
			},

			chunkArray(myArray, chunk_size){
			  
			   var index = 0;
			   
			   var arrayLength = myArray.length;
			   
			   var tempArray = [];
			    
			    for (index = 0; index < arrayLength; index += chunk_size) {
			        let myChunk = myArray.slice(index, index+chunk_size);
			        // Do something if you want with the group
			        tempArray.push(myChunk);
			    }

			    return tempArray;
			}
		}
	};
</script>

<style scoped>
	
	.login-box { width : auto !important;min-width : 600px !important;max-width: 1000px !important; overflow: auto;}

	.field-text.is-danger,.help.is-danger { color: #f22435; }

	.input.is-danger, .input-group.is-danger >.input, .input-icons.is-danger >.input { border-color: #f22435; }
</style>