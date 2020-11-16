# Background
- There are two ways to style your theme:
  - LESS CSS, which is used in Booststrapbase theme.
  - SASS CSS, which is used in Boost theme.
- LESS CSS is compiled using a core `grunt` task and stored in theme/bootstrapbase/style/moodle.css. They are specified using `$THEME->sheets = []` or `$THEME->lessfile = ''`.
- SASS css is compiled using a core `scssphp`. They are specified by using `$THEME->scss = ''`.
- Boost uses [templates](https://docs.moodle.org/dev/Templates) to render the main layouts.
- Since I will be using SASS, I will focus on this mostly.
- In theme Boost all overridden renderers can be found in `theme/boost/classes/output`.
- The default SASS configuration for Boost and child themes is:
```
$THEME->scss = function($theme) {
    return theme_boost_get_main_scss_content($theme);
};
$THEME->extrascsscallback = 'theme_boost_get_extra_scss';
$THEME->prescsscallback = 'theme_boost_get_pre_scss';
$THEME->precompiledcsscallback = 'theme_boost_get_precompiled_css';
$THEME->addblockposition = BLOCK_ADDBLOCK_POSITION_FLATNAV;
```
- You can specify which SASS files you want inside of your `lib.php` script.
  - As an example, we are extending our custom theme from the parent theme called **Elegance**. Here is the code to specify that:
  ```
  function theme_elegance_get_main_scss_content($theme) {
    global $CFG;
    
    $scss = file_get_contents($CFG->dirroot . '/theme/elegance/scss/variables.scss');
    $scss .= file_get_contents($CFG->dirroot . '/theme/boost/scss/fontawesome.scss');
    $scss .= file_get_contents($CFG->dirroot . '/theme/boost/scss/bootstrap.scss');
    $scss .= file_get_contents($CFG->dirroot . '/theme/boost/scss/moodle.scss');
    $scss .= file_get_contents($CFG->dirroot . '/theme/elegance/scss/post.scss');
    
    return $scss;
  }
  ```
  - If you are using the example above, ensure that the following files are in **Elegance**: `theme/elegance/scss/variables.scss` and `theme/elegance/scss/post.scss`.
- Continue at https://docs.moodle.org/dev/Moving_your_theme_to_use_boost_as_a_parent_theme. Read the entire page again.