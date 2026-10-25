1. In EventList, what goes wrong if you use the array index as :key and the list gets filtered while a 
card has local state (for example an open details panel)?

=> The key tells Vue which card is which. With the index, a card’s key is its position, not the event. When the list gets filtered, a different event moves into that position and gets the same key, so Vue reuses the old card. The local state (like an open details panel) then stays at that position and shows up on the wrong event. With :key="e.id", the state follows the event.

2. Why is visible a computed and not a function called from the template? What is the difference when you type in the search box?

=> A computed saves its result and only recalculates when something it uses changes (search, category or events). A function called from the template runs again on every re-render, and visible is used three times in the template (count, v-if, v-for), so it would run three times per render. When you type in the search box, the computed recalculates once per keystroke and reuses the result. It also does no work when something unrelated changes.

3. EventCard emits book instead of changing event.booked. Give two reasons.

=> First, props are read-only, so changing event.booked in the child is bad practice and Vue warns about it. Second, the data belongs to the parent. App owns events and myBookings, so only App knows the rules (is there a seat left? update the header count?). If the card changed the number itself, the data could change from many places and it would be hard to find bugs. With an emit, there is one place, onBook, that decides.

4. You write const { booked } = reactive(event); booked++. The page does not change. Why?

=> const { booked } = reactive(event) copies the number out of the object. After that, booked is just a plain number, and Vue can’t see it change, so the page stays the same. To update the page, you change the property on the object itself: event.booked++.

5. Where does myBookings live, and why not in EventCard or EventList? 

=> It lives in App.vue, because App is the owner of the data and the one that changes it in onBook. The header, which shows the count, is also in App. EventCard shows one event and EventList is only a filtered list of them, so neither should hold the bookings. If it lived in a card, each card would have its own copy, and the header and the other components couldn’t see it. State goes in the lowest component that all the users of it share, and here that is App.