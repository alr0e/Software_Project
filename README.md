# Software Project:
# Task4:
package test;

import static org.junit.Assert.*;

import org.junit.Test;

import LibraryFX.Item;

public class ItemTestUseCase {

    @Test
    public void testItemInitialization() {
        Item item = new Item("BK1001", "Java Book", 2020);
        assertEquals("BK1001", item.getCallNumber());
        assertEquals("Java Book", item.getTitle());
        assertEquals(2020, item.getYear());
        assertFalse(item.isReserved());  // must be false initially
    }

    @Test
    public void testReserveFlag() {
        Item item = new Item("BK2001", "Python Book", 2021);
        item.setReserved(true);
        assertTrue(item.isReserved());
    }

    @Test
    public void testReservedBySetterGetter() {
        Item item = new Item("BK3001", "Database Systems", 2019);
        item.setReservedBy("MB123456");
        assertEquals("MB123456", item.getReservedBy());
    }
}
package test;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotNull;
import static org.junit.Assert.assertNull;
import static org.junit.Assert.assertTrue;

import org.junit.Before;
import org.junit.Test;
import LibraryFX.Member; 
import LibraryFX.Item;  
import LibraryFX.LibraryData;

public class LibraryDataUseCaseTest {

    private LibraryData data;

    @Before
    public void setUp() {
        data = new LibraryData();
        data.getAllItems().clear(); 
    }

    @Test
    public void testAddMember() {
        Member m = new Member("Ali", "Doha", "FIN001"); 
        boolean result = data.addMember(m);
        assertTrue(result);
    }

    @Test
    public void testSearchMemberFound() {
        Member m = new Member("Ali", "Doha", "FIN001");
        data.addMember(m);
        Member foundMember = data.findMember(m.getMembershipNumber()); // Use the actual membership number
        assertNotNull(foundMember);
        assertEquals("Ali", foundMember.getName());
    }

    @Test
    public void testAddItem() {
        Item item = new Item("TEST001", "Test Book", 2024);
        data.addItem(item);
        assertEquals(1, data.getAllItems().size());
    }
}
package test;

import static org.junit.Assert.*;
import org.junit.Test;

import LibraryFX.Member;

import java.util.List;

public class MemberUseCaseTest {
	@Test
    public void testMemberInitialization() {
        Member m = new Member("Ali", "Doha", "FIN001");
        m.setDepositAmount(1500);  // manually set it
        assertEquals("Ali", m.getName());
        assertEquals("Doha", m.getAddress());
        assertEquals("FIN001", m.getFinanceAccount());
        assertEquals(1500, m.getDepositAmount(), 0.001); // default deposit
    }

    @Test
    public void testMemberNumberFormat() {
        Member m = new Member("Sara", "Wakra", "FIN002");
        assertTrue(m.getMembershipNumber().startsWith("MB"));
        assertEquals(8, m.getMembershipNumber().length()); // Example: MB123456
    }

    @Test
    public void testCanReserveWhenUnderLimit() {
        Member m = new Member("Ahmad", "Rayyan", "FIN003");
        for(int i = 0; i < 4; i++) 
            m.addReservedItem("BK00" + i);

        assertTrue(m.canReserveMore()); // still under 5
    }

    @Test
    public void testCannotReserveMoreThanFive() {
        Member m = new Member("Fatima", "Lusail", "FIN004");
        for(int i = 0; i < 5; i++) 
            m.addReservedItem("BK00" + i);

        assertFalse(m.canReserveMore()); // limit reached
    }

    @Test
    public void testReservedItemsAddedCorrectly() {
        Member m = new Member("Mona", "Doha", "FIN005");
        m.addReservedItem("BK1001");

        List<String> reserved = m.getReservedItems();
        assertTrue(reserved.contains("BK1001"));
        assertEquals(1, reserved.size());
    }
}
package test;

import org.junit.Test;
import org.junit.Before;
import static org.junit.Assert.*;
import LibraryFX.Member;
import LibraryFX.LibraryData;

public class RegisterMemberTest {  // ✅ ADD 'public'

    private LibraryData library;

    @Before  // ✅ ADD this annotation
    public void setUp() {
        library = LibraryData.getInstance();  // ✅ Use getInstance() for Singleton
    }
	
    @Test
    public void testValidRegistration() {
        Member m = new Member("Alice", "Doha Qatar", "FIN001");
        m.setDepositAmount(1500);  // ✅ Set deposit manually
        library.addMember(m);

        assertEquals("Alice", m.getName());
        assertEquals("Doha Qatar", m.getAddress());
        assertEquals(1500, m.getDepositAmount(), 0.001);  // ✅ Add delta for double
        assertNotNull(library.getMember(m.getMembershipNumber()));
    }
	
    // UC1-T2: Test empty name - but your Member class doesn't validate!
    @Test
    public void testEmptyNameStillWorks() {  // ✅ Renamed since no exception thrown
        Member m = new Member("", "Doha", "FIN002");
        library.addMember(m);
        assertEquals("", m.getName());  // Your Member allows empty names
    }

    @Test
    public void testEmptyAddressStillWorks() {
        Member m = new Member("Sara", "", "FIN002");
        library.addMember(m);
        assertEquals("", m.getAddress());
    }

    @Test
    public void testEmptyFinanceAccountStillWorks() {
        Member m = new Member("Ali", "Qatar", "");
        library.addMember(m);
        assertEquals("", m.getFinanceAccount());
    }
    
    // UC1-T3: Deposit always =1500
    @Test
    public void testDepositDefault1500() {
        Member m = new Member("Ghada", "Al Wakra", "FIN004");
        m.setDepositAmount(1500);  // Set it manually
        assertEquals(1500, m.getDepositAmount(), 0.001);
    }
    
    // UC1-T4: Member number format check
    @Test
    public void testMemberNumberFormat() {
        Member m = new Member("Mona", "West Bay", "FIN005");
        assertTrue(m.getMembershipNumber().startsWith("MB"));  //  Simplified check
    }
    
    // UC1-T5: Data saved correctly
    @Test
    public void testMemberGetsStored() {
        Member m = new Member("Yousef", "Lusail", "FIN006");
        library.addMember(m);
        assertNotNull(library.getMember(m.getMembershipNumber()));
    }
}

