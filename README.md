# Datakraf PHP Style Guide
This document acts as a guideline in writing comprehensible, sensible and maintainable PHP & Laravel codes across Datakraf Web Applications.

## Localization
Write all methods, functions, properties, routes in English even though the app is in Bahasa or in any languages. Localization should be done in `config/app.php` and translation arrays. This practice ensure the code make sense and avoid any issues with plural and singular form of the model name.

    // good
    public function createComplaint(Complaint $complaint){
    // code...
    }
	
	// bad
	public function createAduan(Aduan $aduan){
	// code...
	}

## Writing Controllers

### Class
Write the class name in `StudlyCaps` format and it should be plural.

    class PostsController{
	// code ...
	}

### Methods

Try to retain the use of default CRUD keywords; `index`,`create`,`show`,`update`,`delete`.
Custom method names should be written in `camelCase` and in verb form. and make it type hinted where possible.

    public function getUserId(int $id){
    // code...
    }
	
	public function favouritePost(int $favourite){
	// code...
	}
	
	public function syncUserInvoice(User $user, Request $request, Invoice $invoice, int $id){
	// code...
	}

	public function getPage(string $url){
	// code...
	}


### Docblocks
Always write Docblock for every method you write. You can use Docblock extension in your IDE to generate one. List out all parameters in `@param` line.

    /**
     * Create user
     *
     * @param Request $request
     * @return void
     */
    public function create(Request $request)
    {
        User::create([
			'name'=>$request->name,
        ]);
    }
  
### Methods Refactoring

Always refactor your method, don't make it huge and difficult to read. Make it simple, stick to DRY and KISS. So instead of this whole code blocks in single method,

    class PostsController{
	    
	    public function create(Request $request){
				$post = new Post();
				$post->title;
				$post->content;
				// upload post attachments
				foreach ($request->file('attachment') as $file) {
            if (!empty($file)) {
               // save the attachment with event title and time as prefix
                $originalFileName = $file->getClientOriginalName();
                $newFileName = time() . '-' . $this->getResumeId() . '-' . $file->getClientOriginalName();
               
               // move the attachments to public/uploads/posts/attachments folder
                $file->move('uploads/posts/attachments', $newFileName);
               
               // create post attachement record in database, attach it to Post ID
               $attachment = new PostAttachment();
               $attachment->post_id = $post->id;
               $attachment->file_name = $newFileName;
               $attachment->save();
            }
        }
	    }
	    $post->save();
	    return redirect()->back();
    }

You can write it like this;

    class PostsController{	    
	    
	    public function create(Request $request){

		    // simplified create method, only three lines :)
		    $post = Post::create($request->all());
		    $this->uploadPostAttachment($request,$post);
		    return redirect()->back();
		    
	    }
		
		public function update(Request $request, $id){
		
			// you can reuse the upload method for update too :)
			Post::find($id)->update($request->all());
			$this->uploadPostAttachment($request, $post);
			return redirect()->back();
			
		}
		
		public function uploadPostAttachment($request, $post){
			
			if (!empty($file)) {
               // save the attachment with event title and time as prefix
                $originalFileName = $file->getClientOriginalName();
                $newFileName = time() . '-' . $this->getResumeId() . '-' . $file->getClientOriginalName();
               
               // move the attachements to public/uploads/posts/attachments folder
                $file->move('uploads/posts/attachments', $newFileName);
               
               // create post attachment record in database, attach it to Post ID
               PostAttachment::create([
					'post_id' => $post->id,
					'file_name' => $newFileName,
               ]);               
            }
		}			
	   
    }

## Configuration
Configuration files must use `kebab-case`.

    config/ 
          blog-module.php

## Commands
The name of artisan command should be `kebab-cased`.

    php artisan delete-all-records

## Route
Public-facing urls must use kebab-case.

    https://datakraf.com/about
    https://datakraf.com/technology-stack
    https://datakraf.com/jobs/frontend-developer

use `camelCase` for route name

    // routes/web.php
    Route::get('open-source', 'OpenSourceController@index')->name('openSource');
    // route name in link 
        <a href="{{ route('openSource') }}"> Open Source </a>

use `camelCase` for route parameters

    Route::get('news/{newsItem}', 'NewsItemsController@index');

## Views
use `camelCase` for view file name.

    resources/ 
		    views/ 
			    openSource.blade.php
##  Validation
Custom validation rule must use `snake_case`

    Validator::extend('organisation_type', function ($attribute, $value) { return OrganisationType::isValid($value); });

## Translation
Translations must be rendered with the `__` function. We should use this over `@lang` in Blade views because `__`can be used in both Blade views and regular PHP code. 

    <h2>{{ __('organization.form.title') }}</h2> 
    
    {!!__('organization.form.description') !!}
