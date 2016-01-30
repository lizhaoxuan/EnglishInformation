#Styling Views on Android 
#Android样式的使用观点


#####原文：http://blog.danlew.net/2014/11/19/styles-on-android/ 

========

*Styles are hard to get right on Android. There's a lot of potential for frustration. The hierarchy easily devolves into spaghetti code. How often have you wanted to change a style but feared you might break something unintentionally by doing so?*

**在Android上，很难正确的使用Styles。这里有很多潜在的挫折。层次结构很容易陷入“意大利面式代码（无头绪代码、形容代码很难读懂）。多久了，你想修改style，但又畏惧担心弄错了某些东西？”**

*After many years of working with Android, I have some rather strong opinions on how best to work with styles. How to work with them without driving myself crazy. A series of rules that helps me hold onto my sanity.*

**经过几年的android工作经验，我在如何更好的使用styles方面有了一些相当健壮的建议。如何使用它们没把我逼疯了。通过一系列的规则，帮助我么年保持理智。**

*This post deals with styling Views, so keep that in mind. Theming, even though it uses the same data structure, is an entirely different beast. While I may write about it someday, I have not yet convinced myself that there is a way to work with themes without going crazy.*

**这篇文章涉及的样式观点,所以记住这一点：Theming,尽管使用相同的数据结构，但它是另一个完全不同的野兽。虽然我可能总有一天要写它，可我还没有说服自己，有一种方法可以使用主题而不疯。**

*Buckle up. This is a long post.*

**系好安全带，这是一篇很长的文章。**


##When to use Styles
##什么时候使用Styles

*The first problem we must address is a simple one: when should you use a style instead of inline attributes?*

**第一个我们必须要提出的问题很简单：什么时候该使用style而不是内嵌属性？**

***Rule #1: Use styles when multiple Views are semantically identical.***

**规则 #1：当有许多相同的View时使用style**


*This rule is best illustrated with a few examples:*

**下面几个例子是这条规则的最好说明**

- *You're creating a calculator. Each button should look the same, so it makes sense to create a CalculatorButton style.*

- **你创建一个计算器。每一个按钮都要看起来一样，因此创建一个CalculatorButton style 是有意义的。**


- *You've got a couple screens with multiple text formats - say, headers, subheaders, and text. You can unify their look by creating Header, Subheader and Text styles.*

**假设你有多个屏幕和多种文字格式，标题，小标题，和文字。你可以用styles统一他们的外观。**

- *You've got thumbnails all over your app. You want them all to look the same. The Thumbnail style is born.*

**缩略图遍布你的app，你希望他们看起来都一样，Thumbnail style产生了。**


*The common thread in all these examples is that these Views are not just using the same attributes - **they play the same role across the app.** Now, when you want to tweak the look/feel of any of these Views, you can just edit the style and change them all at once. It saves you time, effort and keeps your Views consistent.*

=================


*Want to save even more work? Use resource references!*

**想节省更多事情？使用资源引用**

***Rule #2: Use references within styles when appropriate.*
**

规则 #2 在恰当的时候在styles内部使用 references


You could define a style this way:

你习惯这样定义style:

	<style name="MyButton">
    	<item name="android:minWidth">88dp</item>
    	<item name="android:minHeight">48dp</item>
	</style>
	
*What if you wanted minWidth to vary based on screen size? You could just duplicate the style once per screen size (say, sw600dp and sw900dp), but then you're having to duplicate the minHeight attribute as well. What if you want both attributes to change? Suddenly you've got tons of MyButtons defined everywhere, each one duplicating all other attributes. It's a recipe for disaster; it's so easy to forget to change one attribute in one of the many copies.*

**你想要minWidth根据屏幕大小而变化吗？你可以复制style到不同大小的屏幕（比如：sw600dp and sw900dp），但你不得不重复minHeight属性.如果你想要改变这两个属性呢？忽然，你定义了大量的MyButtons在很多地方，每一个都在重复所有的属性，这是一个灾难。很容易忘记改变一个属性的多个副本。**

*Styles are just an alias to a series of attributes. It's a lot easier to just define the style like this:*

**风格是一系列属性的别名。更简单的定义style如下**

	<style name="MyButton">
    	<item name="android:minWidth">@dimen/button_min_width</item>
    	<item name="android:minHeight">@dimen/button_min_height</item>
	</style>
	
*Now you can just modify a single attribute for each resource qualifier. It's absurd to think about duplicating a layout just to change, say, the width of one View in portrait vs landscape. You'd use a dimension for that. The same applies for styles.*

**现在你只需要修改一个属性，就能实现到每一个资源限定符上。一个荒谬的想法是，复制一个布局仅仅只是为了应对改变，比如：在一个view在横竖屏情况下的宽度，你可以在这上面使用使用dimension，而这同样适用于styles**

*I don't mean to imply you should always use resource references in styles; just that you should use it if you need multiple values switched on resource qualifiers.*

**我并没有暗示你应该任何时候都在styles上使用资源引用。只是如果你需要多个重复的值是时候才使用资源限定符**

*This isn't to say that sometimes you won't need to duplicate a style across resource qualifiers, but you can keep it to a minimum. Usually the only reason to do so is because of platform changes (e.g., the change from paddingLeft and paddingRight to paddingStart and paddingEnd).*

This isn't to say that sometimes you won't need to duplicate a style across resource qualifiers,但是你可以把它降到最低。通常这么做的唯一原因是平台的变化。


##Multiple Styles
##多种风格

*It would be wonderful if you could apply multiple styles to a single View, like CSS.*

**如果你可以在一个View上应用多个styles,那将是极好的。就像CSS**

*You can't. Sorry.*

**抱歉，你不能这么做.**

*But you can, in a couple of cases, get an approximation of multiple styles.*

**但是在某些情况下，你可以实现类似多重styles。**

***Rule #3: Use themes to tweak default styles.*
**

***Themes provide ways of defining the default style of many standard widgets. For example, if you want to define the default button for the app, you could do this:***


	<style name="MyTheme">
    	<item name="android:buttonStyle">@style/MyButton</item>
	</style>
	
*If you're just tweaking the default style, the only tricky part is figuring out the parent of your style; you want it to match the appropriate theme for the device, but that varies based on OS version.*

*If you're using an AppCompat theme, you should use their styles as the parent since they handle differences across platforms as well. For example, they have a Spinner style:*

	<style name="MySpinner" parent="Widget.AppCompat.Spinner" />
	
*If the style doesn't exist in AppCompat (or you're not using it), the problem is a bit trickier, since you need the parent to switch based on the theme. Here's an example of a custom Button style that uses Holo normally, but Material when appropriate.*

*You'd put this in* 		**/values/values.xml:**

	<style name="ButtonParent" parent="android:Widget.Holo.Button />

	<style name="ButtonParent.Mine">
    	<item name="android:background">@drawable/my_bg</item>
	</style>
	
*Then, in* **/values-v21/values.xml:**

	<style name="ButtonParent" parent="android:Widget.Material.Button />   
	
*Setting up the correct parent will ensure consistency with both your app and the platform.*

*If you truly want to define all necessary attributes (instead of just tweaking the defaults), you could skip parenting entirely.*

**Rule #4: Use text appearance when possible.**

*TextAppearance allows you to merge two styles for some of the most commonly modified text attributes. Take a look at all your styles: how many of them only modify how the text looks? In those cases, you could instead just modify the TextAppearance.*

*First, you need to define your TextAppearance:*

	<style name="MyTextAppearance" parent="TextAppearance.AppCompat">
    	<item name="android:textColor">#0F0</item>
    	<item name="android:textStyle">italic</item>
	</style>
	
*Notice how I've set a parent - text appearances won't merge, so you need to make sure to define all attributes. You can use any appropriate TextAppearance as the parent.*

*Now you can use it in a TextView:*

	<TextView
    	style="@style/MyStyle"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
    	android:textAppearance="@style/MyTextAppearance" />
    	
*Notice that I can still apply a style to this TextView, getting me a whopping TWO styles for one view! Not as good as true multiple styles, but I'll take what I can get.*

*You can use TextAppearance in any class that extends TextView. That means that EditText, Button, etc. all support text styling.*

## Common Pitfalls

*I've explained all the times when I use styles. Unfortunately, it is easy to abuse styles in ways that will hurt you in the long run. Here's a few anti-patterns to avoid.*

**Rule #5: Do NOT create a style if it's only going to be used once.**

*Styles are an extra layer of abstraction. It adds complexity. You have to lookup the style to see the attributes they apply. As such, I see no reason to use them unless you're going to use the style in multiple places.*

*Which would you rather see when you open up a layout: This?*

	<TextView style="HelloWorldTextView" />
	
*Or this?*

	<TextView
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
    	android:text="@string/hello_world" />
    	
*It's so easy to create a style later if you need to do so. Don't plan ahead too much.*

================

**Rule #6: DO NOT create a style just because multiple Views use the same attributes.
**

*The main reason to use styles is to reduce the number of repeated attributes, right? Why not just use a style whenever multiple Views use the same attributes?*

*The problem with this attitude is that those Views, if they are not used in the same context, may eventually want to differ in how they look. And at that point, your base style becomes difficult to edit without unintended side effects.*

*Think about this scenario: you've got a few TextViews that the same text appearance and background. You think, "hey, I'll create a style, that'll cut down on code duplication." Everything is hunky dory at first, but eventually you want to tweak how some of the TextViews look. The problem is, **by now that style is used all over the place, so you can't edit it without some collateral damage.***

*Fine, you say - I'll just override the style directly in the layout XML. Problem solved. Then it happens again. And again. Eventually that style is meaningless because you're having to override it everywhere. It ends up adding extra work instead of making life easier.*

*That's why I specified in rule #1 that you should use styles when the Views are semantically identical. This ensures that when you change a style, you really do want every View using the style to change.*

##Implicit vs. Explicit Parenting

*Styles support parenting, wherein a child style adopts all attributes of a parent style. It would be rather limiting if they did not.*

*Suppose I want every Button in the app to look the same, so I make a ButtonStyle. Later, I decide half the Buttons should look slightly different - with parenting, I can just create ButtonStyle.Different, getting the base style + the tweaks.*

*It turns out there are two ways of defining parents, implicitly and explicitly:*

	<!-- Our parent style -->
	<style name="Parent" />

	<!-- Implicit parenting, using dot notation -->
	<style name="Parent.Child" />

	<!-- Explicit parenting, using the parent attribute -->
	<style name="Child" parent="Parent" />
	
*Simple enough, right? But what do you think happens here, when we define parents with both methods?*

	<style name="Parent.Child" parent="AnotherParent" />
	
*If you answered that the style has two parents, you are wrong. It turns out that it only has one parent: AnotherParent.*

***Each style can only have one parent, even though there are two ways to define it.** The explicit parent (using the attribute) takes precedence. This leads me to the next rule:*

**Rule #7: DO NOT mix implicit and explicit parenting.**

*Mixing the two is a recipe for confusion. Suppose I have this layout:*

	<Button
    	style="@style/MyWidgets.Button.Awesome"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent" />
    	
*But it turns out that my style is defined thus:*

	<style name="MyWidgets.Button.Awesome" parent="SomethingElse" />
	
*Even though it looks like my Button is based on MyWidgets.Button, it's not! The style name is misleading and the only way to discover that is to do extra work and dig into your style files.*

*The common temptation is to keep using dot notation with explicit parenting so that your styles look hierarchically related:*

	<style name="MyButton" parent="android:Widget.Holo.Button" />
	<style name="MyButton.Borderless" 					parent="android:Widget.Holo.Button.Borderless" />
	
*Object-oriented styles! They look so pretty, right? But looks are all you're getting - **an illusion that styles are related when they are not**. The deception is that MyButton.Borderless is related to MyButton, but they have nothing in common! Let's remove the confusion by removing the dots from the name:*

	<style name="MyButton" parent="android:Widget.Holo.Button" />
	<style name="MyBorderlessButton" parent="android:Widget.Holo.Button.Borderless" /> 
	
*I lose out on the hierarchy looking pretty, but I gain a lot of utility in code.*[^1]

##Styles vs. Themes

*Styles and themes are two different concepts. While styles apply to a single View, themes are applied to a set of Views (or to a whole Activity).

For example, suppose you are using AppCompat and you want to set the primary color for the screen. For this, you must theme the entire Activity:*

	<style name="MyTheme">
    	<style name="colorPrimary">@color/my_primary_color</style>
	</style>
	
*Themes use the same data structure as styles - even using the style tag - but they are, in fact, used in totally different circumstances! They don't operate on the same attributes - for example, you can define a textColor on a View, but there is no textColor attribute for a theme. Likewise, there exists colorPrimary in themes, but in styles they go unused. Thus:*

**Rule #8: DO NOT mix styles and themes.**

*Two common mistakes I've seen:*

1. Applying a theme (as a style) to a View:


		<View style="@android:style/Theme" />
	
*It just makes no sense because a View can't use any of the theme attributes anyways. Nothing happens.*

2. Combining the themes/styles in your hierarchy via parenting. I've seen this as a result of people trying to maintain the illusion of hierarchy using dot notation:

		<style name="MyTheme" />
		<style name="MyTheme.Widget" />
		<style name="MyTheme.Widget.Button" />
		
*Stupid! So, stupid! It does not make any sense and sometimes misfires in strange ways. Just don't do it!*

===============

*As of Lollipop, you can apply themes to a View and all its children[^2]. Even in that circumstance, you shouldn't mix up the two, though you could use them both in parallel:*

	<View 
    	style="@style/MyView"
    	android:theme="@style/MyTheme" />
    	
*AppCompat has a simulacrum of View theming for the Toolbar, but that's all you'll get for a while until Lollipop is the minimum supported version of your app. In other words - you can have fun with this feature in a couple years. :P*

##Conclusion

*The unifying element of these rules are to be careful and thoughtful when using styles. They can save you time, but only if you know when to use them. Haphazard application of styles will eventually lead to frustration.*

Also, like all rules, they are sometimes best when broken. There will always be exceptions, so don't take this article as gospel. [^3]		
	


*Many thanks to Dave Smith for being my editor on this article!*

===================

[^1]: As an aside: If you dig into the AOSP source styles, you'll see that they mix implicit/explicit parenting all the time. That's because framework styles serve a different purpose: as a jumping off point for an app's custom styles. While it makes developer's lives easier, it's all one big illusion. They do a lot of extra work because of it. For example, Widget.Holo.Button does NOT have the parent of Widget.Holo (it's actually Widget.Button), but they maintain the styles so that it works regardless.

[^2]: For more information on View theming, check out this excellent article.

[^3]:For that matter, I am a highly fallible human being, so I hope you don't take anything I take too seriously.