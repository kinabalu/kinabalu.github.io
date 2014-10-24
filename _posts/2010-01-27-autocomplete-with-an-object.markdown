---
layout: post
status: publish
published: true
title: Autocomplete with an Object
author:
  display_name: sswinsburg
  login: sswinsburg
  email: steve.swinsburg@gmail.com
  url: http://steve-on-sakai.blogspot.com
author_login: sswinsburg
author_email: steve.swinsburg@gmail.com
author_url: http://steve-on-sakai.blogspot.com
wordpress_id: 1720
wordpress_url: http://wicketbyexample.com/?p=156
date: '2010-01-27 19:37:01 +0000'
date_gmt: '2010-01-28 02:37:01 +0000'
tags: []
comments: true
---
<p>The Wicket Extensions package provides a neat component called the AutoCompleteTextField.</p>
<p>In a nutshell, this allows you to provide a TextField with a List of Strings and when you start typing, the ones that match the input show up. This is a great component, but consider this scenario:</p>
<p><em>You have a list of contacts, one of which you want to send an email to. Each person has a unique id and a name. You want to search based on their name, but send the message to their id.</em></p>
<pre>
class Person {
  long uuid;
  String displayName;
  String email;
  ...
}</pre>
<p>This is not possible with the AutoCompleteTextField, as it uses a list of Strings and will submit whatever is entered in the text field.<br />
What you need is to be able to separate the key from the value.</p>
<p>Enter the ObjectAutoCompleteTextField. This is a class from <a href="http://wicketstuff.org/confluence/display/STUFFWEB/Home">WicketStuff</a> and allows you to provide a List of Objects and tell the AutoCompleteTextField what value should be the displayed value, and what values should be submitted.</p>
<p>Firstly, the markup is the same as a normal text field:<br />
<code>&lt;input type="text" wicket:id="toField" /&gt;</code></p>
<p>Now lets build the parts we need:</p>
<p><strong>1.</strong> The list of objects:</p>
<pre>
final List contacts = dao.getContacts();
</pre>
<p><strong>2.</strong> The AutoCompletionChoicesProvider:</p>
<pre>
AutoCompletionChoicesProvider provider = new AutoCompletionChoicesProvider() {
	private static final long serialVersionUID = 1L;

	public Iterator getChoices(String input) {
		return logic.getMatchingContacts(contacts, input).iterator();
	}
};
</pre>
<p>This is called whenever the input changes and allows us to get a list of items from our master list that match the input. In this example we already have the list of contacts, so we just need to return an Iterator of the matches for that list. You could use something like this:</p>
<pre>
public List getMatchingContacts(List contacts, String search) {
	List subList = new ArrayList();

	for(Person p : connections){
		if(StringUtils.startsWithIgnoreCase(p.getDisplayName(), search)) {
			if(subList.size() == Constants.MAX_CONNECTIONS_PER_SEARCH) {
				break;
			}
			subList.add(p);
		}
	}
	return subList;
}
</pre>
<p><strong>3.</strong> The ObjectAutoCompleteRenderer:</p>
<pre>
ObjectAutoCompleteRenderer renderer = new ObjectAutoCompleteRenderer(){
	private static final long serialVersionUID = 1L;

	protected String getIdValue(Person p) {
		return p.getUuid();
	}
	protected String getTextValue(Person p) {
		return p.getDisplayName();
	}
};
</pre>
<p>This separates out the key from the value. From the Person class we want to use the uuid as the submitted value<br />
and the displayName as the one that shows up in the textfield and we search based on that.</p>
<p><strong>4.</strong> Putting it all together is the ObjectAutoCompleteBuilder which takes the provider and sets the renderer.</p>
<pre>
ObjectAutoCompleteBuilder builder = new ObjectAutoCompleteBuilder(provider);
builder.autoCompleteRenderer(renderer);
</pre>
<p><strong>5.</strong> Finally, we create the ObjectAutoCompleteField from the builder.</p>
<pre>
ObjectAutoCompleteField autocompleteField = builder.build("toField", new PropertyModel(newMessage, "to"));
final TextField toField = autocompleteField.getSearchTextField();
toField.setRequired(true);
form.add(autocompleteField);
</pre>
<p>We now have a textfield that allows us to search through a list of items but submit a different value associated with each of those items.<br />
<img src="http://www.mysticcoders.com/wp-content/uploads/2010/01/autocomplete.png" alt="autocomplete" width="150" height="176" class="alignnone size-full wp-image-161" /></p>
<p><strong>Advanced tip:</strong><br />
Once we have found an item in the list, it is set into the TextField. If we want to clear the selection, by default the link is a textual [x]. We can override this to be whatever we want, different text, or even an Image.<br />
<img src="http://www.mysticcoders.com/wp-content/uploads/2010/01/autocomplete_search.png" alt="autocomplete_search" width="113" height="36" class="alignnone size-full wp-image-162" /></p>
<p>To use an icon like in the example above, add something like this to the builder:</p>
<pre>
<code>builder.searchLinkImage(new ContextRelativeResource("images/cross.png"));</code>
</pre>
