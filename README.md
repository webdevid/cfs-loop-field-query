# ![Alt text](images/logo.jpg "SECT") CFS Loop Field Query - For Events -

### Modify the Query to multiple dates in a post For [Custom Field Suite](https://wordpress.org/plugins/custom-field-suite/) "Loop Field".

#### Requirements
- - -
* PHP 5.3+
* Activation [Custom Field Suite](https://wordpress.org/plugins/custom-field-suite/) Plugin.
* Create a Loop Field and Date Field in the Loop Field using [CFS](https://wordpress.org/plugins/custom-field-suite/) Plugin.

#### Installation
- - -
 1. Download the cfs-loop-field-query.zip file to your computer.  
 2. Unzip the file.  
 3. Upload the cfs-loop-field-query directory to your /wp-content/plugins/ directory.  
 4. Activate the plugin through the 'Plugins' menu in WordPress.  
 You can access the some setting by going to Settings -> CFS Loop Field Query.
 5. Setting "Post Type Name", "Loop Field Name", "Date Field Name in Loop Feld".  
 ( Optional Field: "Taxonomy Name", "StartTime Field", "FinishTime Field" )  
 That's it:ok_hand: The main query of your select post types will be rewritten.

#### TIP
- - -
* If you want to apply to some existing article, resave the article.  
* This Plugin includes adding Time-Picker Field in CFS.
* Supports Page `is_date()` includes `is_year()` `is_month()` `is_day()`.
* If you have set the 'FinishTime', it does not appear that post when it passes your set time. (Default: The Day Full)

### Usage Example
- - -
You can get a sub query using the `new CFS_LFQ_Query()`

#### Example: Sub Query
    <?php
        $ary	 = array();
        $page    = (get_query_var('paged')) ? get_query_var('paged') : 1;
        $perpage = 10;
        $offset  = ($page - 1) * $perpage;
        $args    = array(
            'posts_per_page' => $perpage
        );
        $query = new CFS_LFQ_Query($args);
    ?>
    <?php if ( $query->have_posts() ) : while ( $query->have_posts() ) : $query->the_post(); ?>
        // something
    <?php endwhile; ?>
    <?php endif;?>
    <?php wp_reset_postdata(); ?>

#### Example: Sub Query For Calendar  
    <?php
        $dates   = array();
        $page    = (get_query_var('paged')) ? get_query_var('paged') : 1;
        $perpage = -1;
        $offset  = ($page - 1) * $perpage;
        $args    = array(
            'posts_per_page' => $perpage,
            'calendar'       => true, // For get the data from not today but first day in this month.
        );
        $query = new CFS_LFQ_Query($args);
    ?>
    <?php if ($query->have_posts()) : while ($query->have_posts()) : $query->the_post(); ?>
    <?php
        $date = date('Ymd', strtotime($post->date));
        array_push($dates, $date);
    ?>
    <?php endwhile; endif; ?>
    <?php wp_reset_postdata(); ?>

    <?php
        // Passing array to cfs_lfq Calendar Class.
        $dates  = array_unique($dates); // Remove some Duplicate Values(Day)
        $date   = new DateTime();
        $months = array();
        for ($i = 0; $i < 3; ++$i) {
            if ($i > 0) {
                $date->modify('first day of +1 month');
            } else {
                $date->modify('first day of this month');
            }
            array_push($months, $date->format('Ymd'));
        }
        cfs_lfq_calendar($dates, $months);  // 3 months Calendar
    ?>
#### Example: Sub Query For Calendar (Your Calendar Class)
    <?php
        $ary	 = array();
        $page    = (get_query_var('paged')) ? get_query_var('paged') : 1;
        $perpage = -1;
    	$offset  = ($page - 1) * $perpage;
        $args    = array(
            'posts_per_page'    => $perpage,
            'calendar'          => true		// For get the data from not today but first day in this month.
        );
        $query = new CFS_LFQ_Query($args);
    ?>
    <?php if ( $query->have_posts() ) : while ( $query->have_posts() ) : $query->the_post(); ?>
    <?php
        $date       = date('Ymd', strtotime($post->date));
        $post_id    = $post->ID;
        $perm       = get_the_permalink();
        $title      = get_the_title();
        array_push($ary, array('date' => $date, 'id' => $post_id, 'permlink' => $perm, 'title' => $title));
    ?>
    <?php endwhile; endif; ?>
    <?php wp_reset_postdata(); ?>

    <?php
        // Passing array to your Calendar Class.
        require_once 'Calendar/Month/Weeks.php';
        calendar($ary, 0);
    ?>
#### Example: Get the "Date", "StartTime" and "FinishTime"
    <div id="date">
        <?php echo date('Y-m-d', strtotime($post->date)); ?>
    </div>
    <time>
        <?php echo date("H:i", strtotime($post->starttime)); ?> ~ <?php echo date("H:i", strtotime($post->finishtime)); ?>
    </time>
### Change log  
#### 1.2.1
 * **Change**: Some field type on Admin-Page from the `<input type="text">` to `<select>`.  

#### 1.2.0
 * **Add**: function for Calendar `cfs_lfq_calendar()`. Using [CalendR](https://github.com/yohang/CalendR) PHP Class.  

#### 1.1.1
 * **Change**: If you have set the 'FinishTime', it does not appear that post when it passes your set time. (Default: the day full)  

#### 1.1.0
 * **Support**: "StartTime" and "FinishTime" for each Date in Loop Field.
