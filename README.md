## osclass (customize)
### [osclasswizards](https://forums.osclass.org/themes/osclasswizards-free-responsive-bootstrap-theme/) theme
> Custom settings, functions, plugins and more...

---

### Make custom fields searchable by user

- First create some custom fields by going in `http://www.olanaxos.gr/ola/oc-admin`

	> Listings > Custom Fields > Add new
	
- Open the `oc-includes/osclass/classes/Rewrite.php` find and rreplace this block of code:
	
	```
	public function extractParams($uri = '')
        {
            $uri_array = explode('?', $uri);
            $length_i = count($uri_array);
            for($var_i = 1;$var_i<$length_i;$var_i++) {
                parse_str($uri_array[$var_i], $parsedVars);
                foreach($parsedVars as $k => $v) {
                    Params::setParam($k, urldecode($v));
                }
            }
        }
 	``` 
        
	with this one:
        
    ```
    public function extractParams($uri = '')
    {
        $uri_array = explode('?', $uri);
        $length_i = count($uri_array);
        for($var_i = 1;$var_i<$length_i;$var_i++) {
            if(preg_match_all('|&([^=]+)=([^&]*)|', '&'.$uri_array[$var_i].'&', $matches)) {
                $length = count($matches[1]);
                for($var_k = 0;$var_k<$length;$var_k++) {
                    Params::setParam($matches[1][$var_k], urldecode($matches[2][$var_k]));
                }
            }
        }
    }
    ```

### Display only selected categories in front page

- open `oc-content/themes/osclasswizards/functions.php`
	
	add this function at the end of the file
	
	```
	<?php //kyr
   function cust_show_only_selected_categories() {
           $categoriesToShow = array(1, 2, 3, 4); // Put here Id's of parent categories or subcategories (no sub-subcategories)
       
           $categories = Category::newInstance()->toTree();
           foreach ($categories as $key => $cat) {
               if (!in_array($cat['pk_i_id'], $categoriesToShow)) {
                   foreach ($cat['categories'] as $key2 => $subcat) {
                       if(!in_array($subcat['pk_i_id'], $categoriesToShow)) {
                           unset($categories[$key]['categories'][$key2]);
                       }
    
                   if (!$categories[$key]['categories']) unset($categories[$key]);
               }
           }
    
           View::newInstance()->_exportVariableToView('categories', $categories);
       }
   }

   //osc_add_hook('before_html', 'cust_show_only_selected_categories');
   ?>
   ```
   
- Now add this line of code in `oc-content/themes/osclasswizards/main.php`

	```
	<?php // kyr Display custom selected categories see functions.php
       echo 'cust_show_only_selected_categories'();
   ?>
	```

### Create custom selected categories on top of main page

- First create a function, open `oc-content/themes/osclasswizards/functions.php`

  add this function at the end of the file
  
  ```
  <?php /* kyr count how manylistings a category has usage: echo cust_count_category_listings(#); where # is the category id*/
  function cust_count_category_listings($cat) {
      $query = Category::newInstance()->findByPrimaryKey($cat);
  return $query['i_num_items'];
  }
  ?>
  ```

- Open the `oc-content/themes/osclasswizards/main.php`
	
	find this lines of code:
	
	```
	<h1 class="title">
      <?php _e('Premium Listings',OSCLASSWIZARDS_THEME_FOLDER);?>
  ```
  
  add above this block of code:
  
  ```
  <h1 class="title">Δωρεάν κατηγορίες για πάντα..</h1>

  <?php // kyr 3 main categories on top
  // kyr Variables
  $cat_1_id="8";
  $cat_1_name="Αγορά εργασίας";
  $cat_2_id="4";
  $cat_2_name="Ενοικιάσεις";
  $cat_3_id="1";
  $cat_3_name="Για πώληση";

  { ?>
  <div class="row">

  <ul class="col-sm-6 col-md-4 grid_list">
  <li>
     <section class="listings">
        <h2><i class="fa fa-shopping-cart"></i> 
            <a class="category %cf%8e" href="http://olanaxos.gr/ola/index.php?page=search&sCategory=<?php echo $cat_1_id ?>"><?php echo $cat_1_name ?></a> <span><?php echo cust_count_category_listings($cat_1_id); ?></span>
        </h2>
     </section>
  </li>
  </ul>
  
    <ul class="col-sm-6 col-md-4 grid_list">
  <li>
     <section class="listings">
        <h2><i class="fa fa-shopping-cart"></i> 
            <a class="category %cf%8e" href="http://olanaxos.gr/ola/index.php?page=search&sCategory=<?php echo $cat_2_id ?>"><?php echo $cat_2_name ?></a> <span><?php echo cust_count_category_listings($cat_2_id); ?></span>
        </h2>
     </section>
  </li>
  </ul>
  
    <ul class="col-sm-6 col-md-4 grid_list">
  <li>
     <section class="listings">
        <h2><i class="fa fa-shopping-cart"></i> 
            <a class="category %cf%8e" href="http://olanaxos.gr/ola/index.php?page=search&sCategory=<?php echo $cat_3_id ?>"><?php echo $cat_3_name ?></a> <span><?php echo cust_count_category_listings($cat_3_id); ?></span>
        </h2>
     </section>
  </li>
  </ul>
  
  </div>
  <?php }
  ?>
  ```
  
  if you want to display subcategories as well and this ex. block between `</h2>` and `</section>` of each category
  
  ```
          <ul>
            <li>
                <a class="category sub-category %ce%af" href="http://olanaxos.gr/ola/index.php?page=search&sCategory=31">Αυτοκίνητα</a> <span>(1)</span>
              </li>
            <li>
                <a class="category sub-category %ce%ac-%ce%ae" href="#">Ανταλλακτικά αυτοκινήτων</a> <span>(0)</span>
              </li>
            <li>
                <a class="category sub-category %ce%ad" href="http://olanaxos.gr/ola/index.php?page=search&sCategory=33">Μοτοσυκλέτες</a> <span>(2)</span>
              </li>
            <li>
                <a class="category sub-category %ce%ac-%ce%af" href="#">Σκάφη - Πλοία</a> <span>(0)</span>
              </li>
            <li>
                <a class="category sub-category rvs-%ce%ad-%cf%8c" href="#">RVs - κατασκηνωτές - Τροχόσπιτα</a> <span>(0)</span>
              </li>
            <li class="last"><a href="http://olanaxos.gr/ola/index.php?page=search&sCategory=2"><strong>See more listings...</strong></a></li>
          </ul>
  ``` 
  
### Refine search

> display result "yamaha" while search for "yam"

- open the `oc-content/themes/osclasswizards/functions.php`

	add this function at the end of the file
	
	```
	<?php //kyr display result "yamaha" wile search for "yam"
   function cust_search_keyword_wildcard_with_username($params) {

       if ($params['sPattern']) {
           $mSearch =  Search::newInstance();
           $query_elements = (array) json_decode($mSearch->toJson());
           $pattern = $query_elements['sPattern'];


           $query_elements['sPattern'] = str_replace(' ', '* ', $pattern) . '*';

           $mSearch->setJsonAlert($query_elements);

           // Search by Username too
           $aPattern = explode(' ', $pattern);
           $userNameCond = '';

           foreach ($aPattern as $word) {
               if ($word) $userNameCond .= sprintf(" || %st_item.s_contact_name LIKE '%s%%'", DB_TABLE_PREFIX, $word);
           }

           $mSearch->addConditions("1 = 1 " . $userNameCond);
           $mSearch->addGroupBy(DB_TABLE_PREFIX.'t_item.pk_i_id'); 
       }
   }

   osc_add_hook('search_conditions', 'cust_search_keyword_wildcard_with_username', 1); 
   ?>
   ```
  
### Remove `city` and `address` from post listing

- open file `oc-content/themes/osclasswizards/item-post.php`

	about line `173` comment out through line `193`
	
	```
<?php /* kyr remove city and address from post listing 
            <div class="form-group">
              <label class="control-label" for="cityArea">
                <?php _e('City Area', OSCLASSWIZARDS_THEME_FOLDER); ?>
              </label>
              <div class="controls">
                <?php ItemForm::city_area_text(osc_user()); ?>
              </div>
            </div>
            <div class="form-group">
              <label class="control-label" for="address">
                <?php _e('Address', OSCLASSWIZARDS_THEME_FOLDER); ?>
              </label>
              <div class="controls">
                <?php ItemForm::address_text(osc_user()); ?>
              </div>
            </div>
          </div>
*/ ?>
	```
	
### How to make new items inactive by default, until the admin activates them

**1. Block new items by default**

Install `More Edit Plugin` and enable the `Moderate all ads (admins have to moderate them)` option
	
or see this ([solution](https://forums.osclass.org/development/how-to-make-new-items-inactive-by-default-until-the-admin-activates-them/msg152642/#msg152642))

**2. Remove succesfull add listing message (attention you edit osclass core file)**

- open file `oc-includes/osclass/controller/item.php `

	go to line ~162 and comment out this 2 lines:
	
	```
	osc_add_flash_ok_message( _m('Check your inbox to validate your listing') );
	osc_add_flash_ok_message( _m('Your listing has been published') );
	```
	
	see:
	
	```
	Session::newInstance()->_clearVariables();
   if($success==1) {
       // osc_add_flash_ok_message( _m('Check your inbox to validate your listing') );
   } else {
       // osc_add_flash_ok_message( _m('Your listing has been published') );
   }
   ```
   
   > Remember to re-apply the above code in each osclass update!!
   

**3. Notify the user via e-mail when it's ad is unblocked** ([source](https://forums.osclass.org/development/how-to-notify-the-user-via-mail-model-when-it%27s-ad-is-unblocked/))

Go to `oc-includes/osclass/ItemActions.php`

and put this code:

```
    /**
     * Enable an item
     * Set s_enabled value to 1, for a given item id
     *
     * @param int $id
     * @return bool
     */
    public function enable($id)
    {
        $result = $this->manager->update(
            array('b_enabled' => 1),
            array('pk_i_id' => $id)
        );

        // updated correctly
        if($result == 1) {
            osc_run_hook( 'enable_item', $id );
            $item = $this->manager->findByPrimaryKey($id);
            if($item['b_active']==1 && $item['b_spam']==0 && !osc_isExpired($item['dt_expiration']) ) {
                $this->_increaseStats($item);
            }
            if($item['b_enabled']==1) {
              osc_run_hook('hook_email_item_unlocked', $item);
            }
            return true;
        }
        return false;
    }
```

instead of this:

```
     /**
     * Enable an item
     * Set s_enabled value to 1, for a given item id
     *
     * @param int $id
     * @return bool
     */
    public function enable($id)
    {
        $result = $this->manager->update(
            array('b_enabled' => 1),
            array('pk_i_id' => $id)
        );

        // updated correctly
        if($result == 1) {
            osc_run_hook( 'enable_item', $id );
            $item = $this->manager->findByPrimaryKey($id);
            if($item['b_active']==1 && $item['b_spam']==0 && !osc_isExpired($item['dt_expiration']) ) {
                $this->_increaseStats($item);
            }
            return true;
        }
        return false;
    }
```

edit the `oc-includes/osclass/emails.php`

after this code:

```
  osc_add_hook('hook_email_item_inquiry', 'fn_email_item_inquiry');
```

add this new function

```
    /* kyr START send email to user when the admin unlocks the item b_enabled=1. (also modify 'oc-includes/osclass/ItemActions.php') */
    function fn_email_item_unlocked($aItem) {
        $id         = $aItem['pk_i_id'];

        $item = Item::newInstance()->findByPrimaryKey( $id );
        View::newInstance()->_exportVariableToView('item', $item);

        $mPages = new Page();
        $aPage  = $mPages->findByInternalName('email_item_inquiry');
        $locale = osc_current_user_locale();

        if( isset($aPage['locale'][$locale]['s_title']) ) {
            $content = $aPage['locale'][$locale];
        } else {
            $content = current($aPage['locale']);
        }

        $item_url = osc_item_url();
        $item_link = '<a href="' . $item_url . '" >' . $item['s_title'] . '</a>';



        $title = "Η διαφήμισή σας εγκρίθηκε - Όλα Σαντορίνη";
        $body  = '<p>Γεια σας ' . $item['s_contact_name'] . ',</p> <p>Η διαφήμισή σας ' . $item_link . ' εγκρίθηκε και έχει αναρτηθεί στο "Ολα Σαντορίνη".</p>';

        $from      = osc_contact_email();
        $from_name = osc_page_title();

        $emailParams = array(
            'from'      => $from,
            'from_name' => $from_name,
            'subject'   => $title,
            'to'        => $item['s_contact_email'],
            'to_name'   => $item['s_contact_name'],
            'body'      => $body,
            'alt_body'  => $body
        );


        osc_sendMail($emailParams);

    }
    osc_add_hook('hook_email_item_unlocked', 'fn_email_item_unlocked');
    /* kyr END */
```        
   
### Carousel/Banner

- Create the folder `banners` in your root of your osclass installation ex. `/httpdocs/osclass/banners`
- Create a folder for each category you want to appear a banner in `/httpdocs/osclass/banners` ex. `banner_id_1` `banner_id_2` where `1,2` is the category id

	open `oc-content/themes/osclasswizards/functions.php`

	and paste the code bellow:

	```
	<?php /* KYR BEGIN CAROUSEL CODE*/ 
	function reklama($i) {
	$cat_id = array(1,2,3,4,5,56,102,109,113,120,122,138,139);
	if (in_array($i, $cat_id))
	$dir = "banner_id_$i" ;
	else $dir = "nodir" ;
	
	$files = scandir("banners/$dir");
	foreach($files as $file) {
	    /* kyr START retrieve url from .txt file */ 
	    $filename = pathinfo($file, PATHINFO_FILENAME); //filename without extension
	    $fileurl = "banners/$dir/.url/$filename.txt";
	    $fh = fopen($fileurl, 'r');
	    $url = fgets($fh);
	    fclose($fh);
	    /* kyr END */
	    if($file !== "." && $file !== ".." && $file !== ".url") {
	        {?>
	        <!-- settings for height and width of banner -->
	        <!-- kyr START old without url --
	        <a href="http://<?php echo $url ?>"><img class="mySlides" src="banners/<?php echo $dir?>/<?php echo $file?>" style="width:100%;height:200px"></a> 
	        <! kyr END -->
	        <!-- kyr START enable url on banner click -->
	        <a href="<?php echo $url ?>" target="_blank"><img class="mySlides" src="banners/<?php echo $dir?>/<?php echo $file?>" style="width:100%;height:200px"></a>
	        <!-- kyr END -->
	        <?php
	        }
	    }
	}
	
	//BEGIN OF HTML CODE INSIDE HTML
	{?>
	<h2 class="w3-center"></h2> <!-- //afini ena oraio keno kato apo to banner-->
	<script>
	var myIndex = 0;
	carousel();
	</script>
	<?php }
	// END OF HTML CODE
	}
	
	function parent($i){
	    $con = mysql_connect(DB_HOST,DB_USER,DB_PASSWORD);
	    if (!$con){
	        die('Could not connect: ' . mysql_error());
	    }
	
	$query="select fk_i_parent_id from ".DB_TABLE_PREFIX."t_category  where pk_i_id='".$i."';";
	mysql_select_db(DB_NAME, $con);
	$result = mysql_query($query);
	
	    while($row = mysql_fetch_array($result)){
	        $vysledek = $row['fk_i_parent_id'] ;
	    }
	
	mysql_close($con);
	    if (strlen($vysledek) == 0) $vysledek = $i;
	        return $vysledek;
	}
	/* KYR END CAROUSEL CODE */ ?>
	```

	open `oc-content/themes/osclasswizards/header.php`

	and paste the code bellow:
	
	```
	<?php /* KYR START OF CATEGORIES CAROUSEL DISPLAY FUNCTION (running as html code)*/
	{?>
	<script>
	function carousel() {
	    var i;
	    var x = document.getElementsByClassName("mySlides");
	    for (i = 0; i < x.length; i++) {
	       x[i].style.display = "none";
	    }
	    myIndex++;
	    if (myIndex > x.length) {myIndex = 1}
	    x[myIndex-1].style.display = "block";
	    setTimeout(carousel, 3000); // Change image every 3 seconds
	}
	</script>
	<?php }
	/* KYR END OF CAROUSEL*/ ?>
	```
	
- Now when you want to display an a banner in some category place your pictures/banners in the category folder id
- Recomended banner size: 1140x200px
- If you want to add links to banners then for each category folder create a `.url` folder and place inside a text file with the same filename as the banner but with `.txt` extension and place inside the `.txt` the link ex. `http://google.com`

**TODO**

1. ~~url support when a user click on the banner (like the slider plugin)~~ **DONE** see above

### Enable lat/lon fields on location (require to edit GoogleMaps plugin)

- edit `oc-content/plugins/google_maps/index.php`

Replace this block of code or commended out

```
    /* kyr START
    function insert_geo_location($item) {
        $itemId = $item['pk_i_id'];
        $aItem = Item::newInstance()->findByPrimaryKey($itemId);
        $sAddress = (isset($aItem['s_address']) ? $aItem['s_address'] : '');
        $sCity = (isset($aItem['s_city']) ? $aItem['s_city'] : '');
        $sRegion = (isset($aItem['s_region']) ? $aItem['s_region'] : '');
        $sCountry = (isset($aItem['s_country']) ? $aItem['s_country'] : '');
        $address = sprintf('%s, %s, %s, %s', $sAddress, $sCity, $sRegion, $sCountry);
        $response = osc_file_get_contents(sprintf('http://maps.googleapis.com/maps/api/geocode/json?address=%s&sensor=false', urlencode($address)));
        $jsonResponse = json_decode($response);
        
        if (isset($jsonResponse->results[0]->geometry->location) && count($jsonResponse->results[0]->geometry->location) > 0)       {
            $location = $jsonResponse->results[0]->geometry->location;
            $lat = $location->lat;
            $lng = $location->lng;
        
            ItemLocation::newInstance()->update (array('d_coord_lat' => $lat
                                                      ,'d_coord_long' => $lng)
                                                ,array('fk_i_item_id' => $itemId));
        }
    }
    /* kyr END */
```

with this

```
    /* kyr START */ 
    function kyr_insert_geo_location($item) {
        $itemId = $item['pk_i_id'];
        if (htmlspecialchars($_POST["latitude"] != "")){
            ItemLocation::newInstance()->update (array('d_coord_lat' => htmlspecialchars($_POST["latitude"])
                                                      ,'d_coord_long' => htmlspecialchars($_POST["longitude"]))
                                                      ,array('fk_i_item_id' => $itemId));
        }
    }
    /* kyr END */
```

then replace this lines

```
    osc_add_hook('posted_item', 'insert_geo_location');
    osc_add_hook('edited_item', 'insert_geo_location');
```

with this

```
    osc_add_hook('posted_item', 'kyr_insert_geo_location');
    osc_add_hook('edited_item', 'kyr_insert_geo_location');
```

[source](https://forums.osclass.org/development/how-to-manually-set-latitude-and-longitude-of-an-item/)

### Theme graphics 

- Category icons

	Go to `https://fontawesome.com/v4.7.0/icons/` and select from `Web Application Icons` 
	
- In `oc-admin` go to (Appearance -> OsclassWizards -> Category Icons) and put the above icon names

  
	
	























