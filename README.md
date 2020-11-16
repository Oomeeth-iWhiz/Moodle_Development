# How to extend a custom theme from Boost
- Create a folder inside of `/themes` folder. Call it `custom`.
- Create a script `/themes/custom/version.php`. This will contain information about our theme
  - Add the following code:
    ```  
    <?php
    // Every file should have GPL and copyright in the header - we skip it in tutorials but you should not skip it for real.
    
    // This line protects the file from being accessed by a URL directly.                                                               
    defined('MOODLE_INTERNAL') || die();                                                                                                
    
    // This is the version of the plugin.                                                                                               
    $plugin->version = '2020081701';                                                                                                    
    
    // This is the version of Moodle this plugin requires.                                                                              
    $plugin->requires = '2020061501.00';                                                                                                   
    
    // This is the component name of the plugin - it always starts with 'theme_'                                                        
    // for themes and should be the same as the name of the folder.                                                                     
    $plugin->component = 'theme_custom';                                                                                                 
    
    // This is a list of plugins, this plugin depends on (and their versions).                                                          
    $plugin->dependencies = [                                                                                                           
        'theme_boost' => '2020061500'                                                                                                   
    ];
    ```
  - Ensure that `$plugin->requires` matches the version of your Moodle installation, which is located at the root directory within `version.php`.
- Create a language file within `/theme/custom/lang/en/theme_custom.php`. The strings located within this file will be translated and displayed in our Moodle theme.
  - Add the following code:
  ```
    <?php
    // Every file should have GPL and copyright in the header - we skip it in tutorials but you should not skip it for real.
    
    // This line protects the file from being accessed by a URL directly.                                                               
    defined('MOODLE_INTERNAL') || die();                                                                                                
    
    // A description shown in the admin theme selector.                                                                                 
    $string['choosereadme'] = 'Theme custom is a child theme of Boost. It adds the ability to upload background customs.';                
    // The name of our plugin.                                                                                                          
    $string['pluginname'] = 'custom';                                                                                                    
    // We need to include a lang string for each block region.                                                                          
    $string['region-side-pre'] = 'Right';
  ```
- Our moodle theme requires 2 images: `/theme/custom/pix/favicon.ico` which will be displayed in the address bar and `/theme/custom/pix/screenshot.jpg` which will be displayed in the theme selector.
- Create a script `/theme/custom/lib.php`. This script contains callbacks which are used by various APIs in Moodle. We will leave this empty for now.
- Create a script `/theme/custom/config.php`.
  - Add the following code:
  ```
  <?php
    
    // Every file should have GPL and copyright in the header - we skip it in tutorials but you should not skip it for real.
    
    // This line protects the file from being accessed by a URL directly.                                                               
    defined('MOODLE_INTERNAL') || die();
    
    // $THEME is defined before this page is included and we can define settings by adding properties to this global object.            
    
    // The first setting we need is the name of the theme. This should be the last part of the component name, and the same             
    // as the directory name for our theme.                                                                                             
    $THEME->name = 'custom';                                                                                                             
    
    // This setting list the style sheets we want to include in our theme. Because we want to use SCSS instead of CSS - we won't        
    // list any style sheets. If we did we would list the name of a file in the /style/ folder for our theme without any css file      
    // extensions.                                                                                                                      
    $THEME->sheets = [];                                                                                                                
    
    // This is a setting that can be used to provide some styling to the content in the TinyMCE text editor. This is no longer the      
    // default text editor and "Atto" does not need this setting so we won't provide anything. If we did it would work the same         
    // as the previous setting - listing a file in the /styles/ folder.                                                                 
    $THEME->editor_sheets = [];                                                                                                         
    
    // This is a critical setting. We want to inherit from theme_boost because it provides a great starting point for SCSS bootstrap4   
    // themes. We could add more than one parent here to inherit from multiple parents, and if we did they would be processed in        
    // order of importance (later themes overriding earlier ones). Things we will inherit from the parent theme include                 
    // styles and mustache templates and some (not all) settings.                                                                       
    $THEME->parents = ['boost'];                                                                                                        
    
    // A dock is a way to take blocks out of the page and put them in a persistent floating area on the side of the page. Boost         
    // does not support a dock so we won't either - but look at bootstrapbase for an example of a theme with a dock.                    
    $THEME->enable_dock = false;                                                                                                        
    
    // This is an old setting used to load specific CSS for some YUI JS. We don't need it in Boost based themes because Boost           
    // provides default styling for the YUI modules that we use. It is not recommended to use this setting anymore.                     
    $THEME->yuicssmodules = array();                                                                                                    
    
    // Most themes will use this rendererfactory as this is the one that allows the theme to override any other renderer.               
    $THEME->rendererfactory = 'theme_overridden_renderer_factory';                                                                      
    
    // This is a list of blocks that are required to exist on all pages for this theme to function correctly. For example               
    // bootstrap base requires the settings and navigation blocks because otherwise there would be no way to navigate to all the        
    // pages in Moodle. Boost does not require these blocks because it provides other ways to navigate built into the theme.            
    $THEME->requiredblocks = '';   
    
    // This is a feature that tells the blocks library not to use the "Add a block" block. We don't want this in boost based themes because
    // it forces a block region into the page when editing is enabled and it takes up too much room.
    $THEME->addblockposition = BLOCK_ADDBLOCK_POSITION_FLATNAV;
  ```
- Run the theme and test it out. It should work as normal. It will look the same as Boost theme.
- You can inherit changes from Boost settings so that any changes made to Boost will not affect Custom. The recommended way to do this is to create a theme settings script in the child theme that matches the settings in the parent theme. You will need to add matching language strings for each of these settings in the language file.
  - Create `/theme/custom/settings.php`. You will see these settings once you install the theme, or in `Site Administration > Appearance > Custom`.
  - Add the following code:
  ```
    <?php
    
    // Every file should have GPL and copyright in the header - we skip it in tutorials but you should not skip it for real.
    
    // This line protects the file from being accessed by a URL directly.                                                               
    defined('MOODLE_INTERNAL') || die();                                                                                                
    
    // This is used for performance, we don't need to know about these settings on every page in Moodle, only when                      
    // we are looking at the admin settings pages.                                                                                      
    if ($ADMIN->fulltree) {                                                                                                             
    
        // Boost provides a nice setting page which splits settings onto separate tabs. We want to use it here.                         
        $settings = new theme_boost_admin_settingspage_tabs('themesettingcustom', get_string('configtitle', 'theme_custom'));             
    
        // Each page is a tab - the first is the "General" tab.                                                                         
        $page = new admin_settingpage('theme_custom_general', get_string('generalsettings', 'theme_custom'));                             
    
        // Replicate the preset setting from boost.                                                                                     
        $name = 'theme_custom/preset';                                                                                                   
        $title = get_string('preset', 'theme_custom');                                                                                   
        $description = get_string('preset_desc', 'theme_custom');                                                                        
        $default = 'default.scss';                                                                                                      
    
        // We list files in our own file area to add to the drop down. We will provide our own function to                              
        // load all the presets from the correct paths.                                                                                 
        $context = context_system::instance();                                                                                          
        $fs = get_file_storage();                                                                                                       
        $files = $fs->get_area_files($context->id, 'theme_custom', 'preset', 0, 'itemid, filepath, filename', false);                    
    
        $choices = [];                                                                                                                  
        foreach ($files as $file) {                                                                                                     
            $choices[$file->get_filename()] = $file->get_filename();                                                                    
        }                                                                                                                               
        // These are the built in presets from Boost.                                                                                   
        $choices['default.scss'] = 'default.scss';                                                                                      
        $choices['plain.scss'] = 'plain.scss';                                                                                          
    
        $setting = new admin_setting_configselect($name, $title, $description, $default, $choices);                                     
        $setting->set_updatedcallback('theme_reset_all_caches');                                                                        
        $page->add($setting);                                                                                                           
    
        // Preset files setting.                                                                                                        
        $name = 'theme_custom/presetfiles';                                                                                              
        $title = get_string('presetfiles','theme_custom');                                                                               
        $description = get_string('presetfiles_desc', 'theme_custom');                                                                   
    
        $setting = new admin_setting_configstoredfile($name, $title, $description, 'preset', 0,                                         
            array('maxfiles' => 20, 'accepted_types' => array('.scss')));                                                               
        $page->add($setting);     
    
        // Variable $brand-color.                                                                                                       
        // We use an empty default value because the default colour should come from the preset.                                        
        $name = 'theme_custom/brandcolor';                                                                                               
        $title = get_string('brandcolor', 'theme_custom');                                                                               
        $description = get_string('brandcolor_desc', 'theme_custom');                                                                    
        $setting = new admin_setting_configcolourpicker($name, $title, $description, '');                                               
        $setting->set_updatedcallback('theme_reset_all_caches');                                                                        
        $page->add($setting);                                                                                                           
    
        // Must add the page after definiting all the settings!                                                                         
        $settings->add($page);                                                                                                          
    
        // Advanced settings.                                                                                                           
        $page = new admin_settingpage('theme_custom_advanced', get_string('advancedsettings', 'theme_custom'));                           
    
        // Raw SCSS to include before the content.                                                                                      
        $setting = new admin_setting_configtextarea('theme_custom/scsspre',                                                              
            get_string('rawscsspre', 'theme_custom'), get_string('rawscsspre_desc', 'theme_custom'), '', PARAM_RAW);                      
        $setting->set_updatedcallback('theme_reset_all_caches');                                                                        
        $page->add($setting);                                                                                                           
    
        // Raw SCSS to include after the content.                                                                                       
        $setting = new admin_setting_configtextarea('theme_custom/scss', get_string('rawscss', 'theme_custom'),                           
            get_string('rawscss_desc', 'theme_custom'), '', PARAM_RAW);                                                                  
        $setting->set_updatedcallback('theme_reset_all_caches');                                                                        
        $page->add($setting);                                                                                                           
    
        $settings->add($page);                                                                                                          
    }
  ```
- Replace `/theme/custom/lang/en/theme_custom.php` with the following code:
  ```
  <?php
    
    // Every file should have GPL and copyright in the header - we skip it in tutorials but you should not skip it for real.
    
    // This line protects the file from being accessed by a URL directly.                                                               
    defined('MOODLE_INTERNAL') || die();
    // The name of the second tab in the theme settings.                                                                                
    $string['advancedsettings'] = 'Advanced settings';                                                                                  
    // The brand colour setting.                                                                                                        
    $string['brandcolor'] = 'Brand colour';                                                                                             
    // The brand colour setting description.                                                                                            
    $string['brandcolor_desc'] = 'The accent colour.';     
    // A description shown in the admin theme selector.                                                                                 
    $string['choosereadme'] = 'Theme custom is a child theme of Boost. It adds the ability to upload background customs.';                
    // Name of the settings pages.                                                                                                      
    $string['configtitle'] = 'custom settings';                                                                                          
    // Name of the first settings tab.                                                                                                  
    $string['generalsettings'] = 'General settings';                                                                                    
    // The name of our plugin.                                                                                                          
    $string['pluginname'] = 'custom';                                                                                                    
    // Preset files setting.                                                                                                            
    $string['presetfiles'] = 'Additional theme preset files';                                                                           
    // Preset files help text.                                                                                                          
    $string['presetfiles_desc'] = 'Preset files can be used to dramatically alter the appearance of the theme. See <a href=https://docs.moodle.org/dev/Boost_Presets>Boost presets</a> for information on creating and sharing your own preset files, and see the <a href=http://moodle.net/boost>Presets repository</a> for presets that others have shared.';
    // Preset setting.                                                                                                                  
    $string['preset'] = 'Theme preset';                                                                                                 
    // Preset help text.                                                                                                                
    $string['preset_desc'] = 'Pick a preset to broadly change the look of the theme.';                                                  
    // Raw SCSS setting.                                                                                                                
    $string['rawscss'] = 'Raw SCSS';                                                                                                    
    // Raw SCSS setting help text.                                                                                                      
    $string['rawscss_desc'] = 'Use this field to provide SCSS or CSS code which will be injected at the end of the style sheet.';       
    // Raw initial SCSS setting.                                                                                                        
    $string['rawscsspre'] = 'Raw initial SCSS';                                                                                         
    // Raw initial SCSS setting help text.                                                                                              
    $string['rawscsspre_desc'] = 'In this field you can provide initialising SCSS code, it will be injected before everything else. Most of the time you will use this setting to define variables.';
    // We need to include a lang string for each block region.                                                                          
    $string['region-side-pre'] = 'Right';
  ```
- Add the following to the end of `/theme/custom/config.php`:
```
// This is the function that returns the SCSS source for the main file in our theme. We override the boost version because          
// we want to allow presets uploaded to our own theme file area to be selected in the preset list.                                  
$THEME->scss = function($theme) {                                                                                                   
    return theme_custom_get_main_scss_content($theme);                                                                               
};
```
- Add the following to `/theme/custom/lib.php`:
```
<?php
  // Every file should have GPL and copyright in the header - we skip it in tutorials but you should not skip it for real.
    
  // This line protects the file from being accessed by a URL directly.                                                               
  defined('MOODLE_INTERNAL') || die();                                                                                                
    
  function theme_custom_get_main_scss_content($theme) {                                                                                
    global $CFG;                                                                                                                    
  
    $scss = '';                                                                                                                     
    $filename = !empty($theme->settings->preset) ? $theme->settings->preset : null;                                                 
    $fs = get_file_storage();                                                                                                       
  
    $context = context_system::instance();                                                                                          
    if ($filename == 'default.scss') {                                                                                              
        // We still load the default preset files directly from the boost theme. No sense in duplicating them.                      
        $scss .= file_get_contents($CFG->dirroot . '/theme/boost/scss/preset/default.scss');                                        
    } else if ($filename == 'plain.scss') {                                                                                         
        // We still load the default preset files directly from the boost theme. No sense in duplicating them.                      
        $scss .= file_get_contents($CFG->dirroot . '/theme/boost/scss/preset/plain.scss');                                          
  
    } else if ($filename && ($presetfile = $fs->get_file($context->id, 'theme_custom', 'preset', 0, '/', $filename))) {              
        // This preset file was fetched from the file area for theme_custom and not theme_boost (see the line above).                
        $scss .= $presetfile->get_content();                                                                                        
    } else {                                                                                                                        
        // Safety fallback - maybe new installs etc.                                                                                
        $scss .= file_get_contents($CFG->dirroot . '/theme/boost/scss/preset/default.scss');                                        
    }                                                                                                                                       
  
    return $scss;                                                                                                                   
  }
```
  - This will get the `scss` styling file and return it `$THEME->scss` in `/theme/custom/config.php`.
- We will now add a setting which will allow us to upload a background image to the login page
- In `/theme/custom/settings.php` replace the last line `$settings->add($page);` with:
```
// Login page background setting.                                                                                               
// We use variables for readability.                                                                                            
$name = 'theme_custom/loginbackgroundimage';                                                                                     
$title = get_string('loginbackgroundimage', 'theme_custom');                                                                     
$description = get_string('loginbackgroundimage_desc', 'theme_custom');                                                          
// This creates the new setting.                                                                                                
$setting = new admin_setting_configstoredfile($name, $title, $description, 'loginbackgroundimage');                             
// This means that theme caches will automatically be cleared when this setting is changed.                                     
$setting->set_updatedcallback('theme_reset_all_caches');                                                                        
// We always have to add the setting to a page for it to have any effect.                                                       
$page->add($setting);
$settings->add($page);
```
- Add the following to the end of `/theme/custom/lang/en/theme_custom.php`:
```
// Background image for login page.                                                                                                 
$string['loginbackgroundimage'] = 'Login page background image';                                                                    
// Background image for login page.                                                                                                 
$string['loginbackgroundimage_desc'] = 'An image that will be stretched to fill the background of the login page.';
```
- We need to update the theme to set this image background on the login page. In `/theme/custom/lib.php`, replace `return $scss;` with the following:
```
// Pre CSS - this is loaded AFTER any prescss from the setting but before the main scss.                                        
$pre = file_get_contents($CFG->dirroot . '/theme/custom/scss/pre.scss');                                                         
// Post CSS - this is loaded AFTER the main scss but before the extra scss from the setting.                                    
$post = file_get_contents($CFG->dirroot . '/theme/custom/scss/post.scss');                                                       
 
// Combine them together.                                                                                                       
return $pre . "\n" . $scss . "\n" . $post;
```
- Create 2 `scss` scripts: `/theme/custom/scss/pre.scss` and `/theme/custom/scss/post.scss`.
  - We need to be able to style our background image on the login page. Add the following code to `/theme/custom/scss/post.scss`:
  ```
    body.pagelayout-login {                                                                                                             
    background-image: url([[pix:theme_custom|loginbackgroundimage]]);                                                                
    background-size: cover;                                                                                                         
    }
  ```
- Add the following to the end of `/theme/custom/lib.php` which will store our uploaded background image:
```
function theme_custom_update_settings_images($settingname) {                                                                         
    global $CFG;                                                                                                                    
 
    // The setting name that was updated comes as a string like 's_theme_custom_loginbackgroundimage'.                               
    // We split it on '_' characters.                                                                                               
    $parts = explode('_', $settingname);                                                                                            
    // And get the last one to get the setting name..                                                                               
    $settingname = end($parts);                                                                                                     
 
    // Admin settings are stored in system context.                                                                                 
    $syscontext = context_system::instance();                                                                                       
    // This is the component name the setting is stored in.                                                                         
    $component = 'theme_custom';                                                                                                     
 
    // This is the value of the admin setting which is the filename of the uploaded file.                                           
    $filename = get_config($component, $settingname);                                                                               
    // We extract the file extension because we want to preserve it.                                                                
    $extension = substr($filename, strrpos($filename, '.') + 1);                                                                    
 
    // This is the path in the moodle internal file system.                                                                         
    $fullpath = "/{$syscontext->id}/{$component}/{$settingname}/0{$filename}";                                                      
    // Get an instance of the moodle file storage.                                                                                  
    $fs = get_file_storage();                                                                                                       
    // This is an efficient way to get a file if we know the exact path.                                                            
    if ($file = $fs->get_file_by_hash(sha1($fullpath))) {                                                                           
        // We got the stored file - copy it to dataroot.                                                                            
        // This location matches the searched for location in theme_config::resolve_image_location.                                 
        $pathname = $CFG->dataroot . '/pix_plugins/theme/custom/' . $settingname . '.' . $extension;                                 
 
        // This pattern matches any previous files with maybe different file extensions.                                            
        $pathpattern = $CFG->dataroot . '/pix_plugins/theme/custom/' . $settingname . '.*';                                          
 
        // Make sure this dir exists.                                                                                               
        @mkdir($CFG->dataroot . '/pix_plugins/theme/custom/', $CFG->directorypermissions, true);                                      
 
        // Delete any existing files for this setting.                                                                              
        foreach (glob($pathpattern) as $filename) {                                                                                 
            @unlink($filename);                                                                                                     
        }                                                                                                                           
 
        // Copy the current file to this location.                                                                                  
        $file->copy_content_to($pathname);                                                                                          
    }                                                                                                                               
 
    // Reset theme caches.                                                                                                          
    theme_reset_all_caches();                                                                                                       
}
```
- Finally, replace the last method `$setting->set_updatedcallback('theme_reset_all_caches');` from `/theme/custom/settings.php` with `$setting->set_updatedcallback('theme_custom_update_settings_images');`.
- Reinstall the theme and have a look at the settings page. You can now upload a background image for the login page.
  - Alternatively, go to `Site Administration > Appearance > Custom` **AFTER** switching to Custom theme.