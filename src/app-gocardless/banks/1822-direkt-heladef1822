import Fallback from './integration-bank.js';

import d from 'date-fns';
import { printIban } from '../utils.js';
import { formatPayeeName } from '../../util/payee-name.js';

/** @type {import('./bank.interface.js').IBank} */
export default {
  ...Fallback,

  institutionIds: ['DIREKT_HELADEF1822'],

  accessValidForDays: 90,

  normalizeAccount(account) {
    return {
      account_id: account.id,
      institution: account.institution,
      mask: account.iban.slice(-4),
      iban: account.iban,
      name: [account.name, printIban(account)].join(' '),
      official_name: account.product,
      type: 'checking',
    };
  },

  normalizeTransaction(transaction, _booked) {
    const date =
      transaction.bookingDate ||
      transaction.bookingDateTime ||
      transaction.valueDate ||
      transaction.valueDateTime;

    // If we couldn't find a valid date field we filter out this transaction
    // and hope that we will import it again once the bank has processed the
    // transaction further.
    if (!date) {
      return null;
    }

    let remittanceInformationUnstructured;

    if (transaction.remittanceInformationUnstructured) {
      remittanceInformationUnstructured =
        transaction.remittanceInformationUnstructured;
    } else if (transaction.remittanceInformationStructured) {
      remittanceInformationUnstructured =
        transaction.remittanceInformationStructured;
    } else if (transaction.remittanceInformationStructuredArray?.length > 0) {
      remittanceInformationUnstructured =
        transaction.remittanceInformationStructuredArray?.join(' ');
    }

    transaction.remittanceInformationUnstructured =
      remittanceInformationUnstructured;

    return {
      ...transaction,
      payeeName: formatPayeeName(transaction),
      date: d.format(d.parseISO(date), 'yyyy-MM-dd'),
    };
  },
};
